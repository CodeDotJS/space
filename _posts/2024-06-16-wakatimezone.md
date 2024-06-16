---
layout: post
title: "WakaTime Date Discrepancy"
tags: [python, date, time, server, wakatime, timezone]
---

I decided to add another feature to the [Cool](../updates) section, which is to show my coding stats, pulled from [Wakatime](https://wakatime.com) -- mainly displaying the time spent in my text editor. The idea is fairly simple, as was the implementation.

```bash
$ curl 'https://wakatime.com/api/v1/users/current/durations?date=2024-06-16' -H
```

```json
{
  "data": [
    {
      "color": null,
      "duration": 4105.670268,
      "project": "ir8x.space",
      "time": 1718481323.908214
    },
    {
      "color": null,
      "duration": 386.632194,
      "project": "ir8x.services",
      "time": 1718485429.578482
    }
  ],
  "end": "2024-06-16T18:29:59Z",
  "start": "2024-06-15T18:30:00Z",
  "timezone": "Asia/Kolkata"
}
```

The `duration` field in each object is in seconds. So, I first needed to sum up all the durations and convert them into hours and minutes because I was planning to chart things out, but I wasn't sure.

```python
if response.status_code == 200:
  data = response.json()
  seconds = sum(item['duration'] for item in data['data'])
  hours = seconds // 3600
  minutes = (seconds % 3600) // 60
```

The initial plan was to only show the time for the current date, but I thought it'd be nice if I could pull the weekly data. To do that, all I had to do was subtract days from the current date and send a request for each day.

```python
from datetime import datetime, timedelta

def get_dates():
  today = datetime.now().date()
  dates = [(today - timedelta(days=i)).isoformat() for i in range(7)]
  return dates

get_dates()

['2024-06-16', '2024-06-15', '2024-06-14', '2024-06-13', '2024-06-12', '2024-06-11', '2024-06-10']
```

Since I wanted it to be fast, the best approach was to create a list of tasks, with each task being a call to `fetch_data_for_date(date)` for a specific date.

```python
base_url = 'https://wakatime.com/api/v1/users/current/durations?date={}&paywalled=true'

async def fetch_data_for_date(date):
  async with httpx.AsyncClient() as client:
    response = await client.get(base_url.format(date), headers=headers)
```

By using `asyncio.gather`, we can run all tasks concurrently and return a list of results from the completed tasks. That's it.

```python
async def fetch_all_data(dates):
    tasks = [fetch_data_for_date(date) for date in dates]
    return await asyncio.gather(*tasks)
```

Everything worked without any issues, and the script is returning the required data ---

```json
[
  {
    "date": "2024-06-16",
    "hours": 7,
    "minutes": 53
  },
  {
    "date": "2024-06-15",
    "hours": 15,
    "minutes": 17
  },
  // previous days
]
```

So far, so good, but I noticed something on the 14<sup>th</sup> of June --- the chart was showing data only up to the 13<sup>th</sup>. This was strange because when I ran my Flask API locally, it was returning data up to the 14<sup>th</sup>. It didn't make any sense.

- Response --- Vercel

```
$ curl https://ir8x.vercel.app/coding | jq

[
  {
    "date": "2024-06-13",
    "hours": 14,
    "minutes": 16
  },
  {
    "date": "2024-06-12",
    "hours": 17,
    "minutes": 13
  }
]

```

- Response --- localhost

```
$ curl http://127.0.0.1:5000/coding | jq

[
  {
    "date": "2024-06-14",
    "hours": 17,
    "minutes": 20
  },
  {
    "date": "2024-06-13",
    "hours": 14,
    "minutes": 16
  }
]

```

I was confused about what was happening. The code was functional, and everything was working as it should, so why was there a difference in the response while the code remained the same? I thought about it for a while and decided to check the response header.

```
$ curl -X HEAD -I https://ir8x.vercel.app/coding

HTTP/2 200
access-control-allow-origin: *
age: 0
cache-control: public, max-age=0, must-revalidate
content-type: application/json
date: Thu, 13 Jun 2024 22:19:50 GMT
server: Vercel
strict-transport-security: max-age=63072000; includeSubDomains; preload
x-vercel-cache: MISS
x-vercel-id: bom1::iad1::qwvhv-1718508856184-67fa70ffdc36
content-length: 0
```

At this point, I had realized that I'm facing time discrepancy. The important thing to notice here is __`x-vercel-id: bom1::iad1::qwvhv-1718508856184-67fa70ffdc36`__, which indicates that -

1. The deployment is using two regions: `bom1` and `iad1`.
  - `bom1` corresponds to Mumbai, India.
  - `iad1` corresponds to Washington, D.C., USA.

2. The request was received by the Mumbai edge location (`bom1`) and then *possibly* routed or processed further by the Washington, D.C. location (`iad1`), unless it's a fallback region which I read somewhere in the docs a while ago.

My reasoning involved a bit of an assumption - I got confused because I thought that since the request is served from the `bom1` region, the `get_dates` function in my code should use the timezone for Mumbai (Asia/Kolkata, `UTC+05:30`). I assumed that the primary deployment region (Washington, D.C.) wouldn't affect the runtime behavior of the code served from Mumbai. However, upon stumbling upon the Vercel docs on [Configuring Regions for Vercel Functions](https://vercel.com/docs/functions/configuring-functions/region), I found ---


> By default, Serverless Functions execute in Washington, D.C., USA (iad1) for all new projects to ensure they are located close to most external data sources, which are hosted on the East Coast of the USA.

This made everything clear, and I learned that two things are happening:

1. **Request Handling**: The request is received and served from the `bom1` edge location.
2. **Code Execution**: The primary execution of the serverless functions happens in Washington, D.C., USA (`iad1`).

So even though the request is served from Mumbai (`bom1`), the timezone used for datetime functions would reflect the primary deployment region (Washington, D.C., `iad1`).

In any case, since my initial `get_dates` relied on the remote server's timezone, I had to tweak it a little to ensure it fetches the data from WakaTime with my timezone - `Asia/Kolkata`.

```python
from datetime import datetime, timedelta
import pytz

def get_dates(timezone='Asia/Kolkata'):
    # Get the current UTC time
    today_utc = datetime.utcnow().replace(tzinfo=pytz.utc)

    # Convert the UTC time to the specified timezone
    today_local = today_utc.astimezone(pytz.timezone(timezone))

    # Generate a list of dates for the past 7 days in ISO and "YYYY-MM-DD" format
    date_timestamps = [(today_local - timedelta(days=i)).isoformat() for i in range(7)]
    dates = [(today_local - timedelta(days=i)).strftime('%Y-%m-%d') for i in range(7)]

    return {'timestamps': date_timestamps, 'dates': dates}

print(get_dates())

# {'timestamps': ['2024-06-16T22:25:06.020235+05:30', '2024-06-15T22:25:06.020235+05:30', '2024-06-14T22:25:06.020235+05:30', '2024-06-13T22:25:06.020235+05:30', '2024-06-12T22:25:06.020235+05:30', '2024-06-11T22:25:06.020235+05:30', '2024-06-10T22:25:06.020235+05:30'], 'dates': ['2024-06-16', '2024-06-15', '2024-06-14', '2024-06-13', '2024-06-12', '2024-06-11', '2024-06-10']}
```

It all circled back to the first step I took when I started building this service. I completely ignored the `timezone` from the initial response. My bad!

```json
  "end": "2024-06-16T18:29:59Z",
  "start": "2024-06-15T18:30:00Z",
  "timezone": "Asia/Kolkata"
```

<br>

__*Result*__
{: .center}

{% include wakatime.html %}

<canvas id="codingChart"></canvas>

Live data pulled from WakaTime.
{: .center}

