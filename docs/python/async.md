# Async

## Overview

Asynchronous programming lets Python start an operation that may take time—such as a network request, database query, or file operation—and work on something else while waiting.

The main keywords are:

async → defines an asynchronous function
await → waits for an async operation without blocking the event loop
asyncio → Python's standard library for asynchronous programming

This is especially useful for I/O-bound work:

- HTTP/API requests
- databases
- sockets
- network services
- WebSockets
- waiting for external services
- many concurrent connections

---

## Example 1

```python
import asyncio
import aiohttp


async def get_users():
    url = "https://jsonplaceholder.typicode.com/users"

    async with aiohttp.ClientSession() as session:
        async with session.get(url) as response:
            users = await response.json()

            for user in users:
                print(user["name"])


asyncio.run(get_users())
```

---

## Example 2

```python
import asyncio
import aiohttp


async def get_user(session, user_id):
    url = f"https://jsonplaceholder.typicode.com/users/{user_id}"

    async with session.get(url) as response:
        return await response.json()


async def main():

    async with aiohttp.ClientSession() as session:

        users = await asyncio.gather(
            get_user(session, 1),
            get_user(session, 2),
            get_user(session, 3)
        )

        for user in users:
            print(user["name"])


asyncio.run(main())
```

---

## Error handling

```python
import asyncio
import aiohttp


async def get_user(session, user_id):

    url = f"https://jsonplaceholder.typicode.com/users/{user_id}"

    try:
        async with session.get(url) as response:

            response.raise_for_status()

            return await response.json()

    except aiohttp.ClientError as error:
        print(f"Request failed: {error}")
        return None


async def main():

    async with aiohttp.ClientSession() as session:

        users = await asyncio.gather(
            get_user(session, 1),
            get_user(session, 2),
            get_user(session, 3)
        )

        for user in users:
            if user:
                print(user["name"])


asyncio.run(main())
```