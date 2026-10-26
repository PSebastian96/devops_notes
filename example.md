### 1. Django test commands

| Purpose                       | Command                                                                              |
| ----------------------------- | ------------------------------------------------------------------------------------ |
| Run all Django tests          | `python manage.py test`                                                              |
| Run tests for one app         | `python manage.py test orders`                                                       |
| Run a specific test module    | `python manage.py test orders.tests.test_tasks`                                      |
| Run one test class            | `python manage.py test orders.tests.test_tasks.ProcessOrderTests`                    |
| Run one test method           | `python manage.py test orders.tests.test_tasks.ProcessOrderTests.test_process_order` |
| Run tests with verbose output | `python manage.py test --verbosity=2`                                                |
| Check Django configuration    | `python manage.py check`                                                             |
| Check production settings     | `python manage.py check --deploy`                                                    |

---




### 3. Test Celery tasks

| Purpose                                     | Command or code                                        |
| ------------------------------------------- | ------------------------------------------------------ |
| Open Django shell                           | `python manage.py shell`                               |
| Execute a task function directly            | `process_order(123)`                                   |
| Execute a task synchronously through Celery | `process_order.apply(args=(123,))`                     |
| Queue a task through the broker             | `process_order.delay(123)`                             |
| Queue a task with keyword arguments         | `process_order.delay(order_id=123)`                    |
| Queue a task with explicit options          | `process_order.apply_async(args=(123,), countdown=10)` |
