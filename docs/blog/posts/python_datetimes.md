---
title: Python datetime (Part 1 of many)
date: "2021-10-03"
description: "Learning subtle differences in datetimes"
---

## Background

This week I was tackling a bug that involved datetime and timezones. After working on it for a while I needed help. Not necessarily a hard problem but timezones are confusing and can quickly make the problem worse.

Luckily at my work, [Octopus Energy](https://www.octopusenergy.com), we've great team members that are always there to help. And **David Seddon** offerred to help.

David, if you're reading this blog, Thank you 🙏

You should check out his [blog](https://seddonym.me/), he has some intereseting topics there.

## What was my problem?

If a given datetime is in UTC, will it still be the same if we convert it to central timezone (America/Chicago)?

## Answer

Yes it will be. In David's words (paraphrasing):

_Two datetimes will be equal to each other if they are in the same moment._

That threw me off 🤯

But after spending some time and really undestanding what he meant, it finally clicked.

**UTC**: Is a time standard by which we represent a particular instance of time globally. So basically if a timezone let's say "X" is represented in UTC, it means "X" is expressed in a positive or negative offset from UTC ([from wikipedia](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)).

For example:

```python
CENTRAL_TIMEZONE = pytz.timezone("America/Chicago")

def compare_utc_and_central() -> bool:
    central_datetime = datetime(2021, 10, 5, tzinfo=CENTRAL_TIMEZONE)
    utc_datetime = central_datetime.astimezone(timezone.utc)

    print(central_datetime) # prints: 2021-10-05 00:00:00-05:51
    print(utc_datetime) # prints: 2021-10-05 05:51:00+00:00

    return central_datetime == utc_datetime # Returns True
```

<br/>

Another example: Let's say you have a UTC datetime and you want to convert it into a central timezone, but this time without calling the `astimezone`. Also useful if you've tests and want to
assert at specific time.

```python
import freezegun
import pytz

from datetime import date, datetime, timezone
from typing import Tuple


def get_central_datetime_using_utc_datetime(utc_datetime:datetime) -> bool:
    """
    Notice the usage of freezegun. We have to make sure that we are in the same moment
    and freezegun allows us to do that.
    """
    with freezegun.freeze_time(utc_datetime):
        central_datetime = datetime.now(tz=CENTRAL_TIMEZONE)

    return central_datetime == utc_datetime # Returns True
```

<br/>

In summary, this was a great learning experience for me. And because of David, I can say that I somewhat have an understanding of timezones in python. Not all obviously, hence the title of this blog is Part 1 of many. There's still a lot to learn.

<br/>
