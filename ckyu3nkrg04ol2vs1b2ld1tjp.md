## Your test cases should fail

Writing tests are not enough. If the tests don't fail when a bug is introduced then what's the point of writing tests. Your test should fail when the application doesn't meet the requirements. In this article, we look at an example, then improve its test and later end with a few guidelines for writing tests.

### Example

Suppose we are making an API that returns information about a user.

#### API

```sh
# GET /users/defunkt
$ curl https://api.github.com/users/defunkt

> {
>   "login": "defunkt",
>   "id": 2,
>   "node_id": "MDQ6VXNlcjI=",
>   "avatar_url": "https://avatars.githubusercontent.com/u/2?v=4",
>   "gravatar_id": "",
>   "url": "https://api.github.com/users/defunkt",
>   "html_url": "https://github.com/defunkt",
>   ...
> }
```

#### Test for the API

The following code sends a request to `/user/defunkt`. The test passes if the status code of the response is 200 else it fails.

```js
test('if /user/:id API works', () => {
  const response = request(app).get('/user/defunkt');

  expect(response.statusCode).toBe(200);
})
```

### What's wrong with the above test?

Imagine that while developing, someone makes a mistake and `node_id` is now missing from the API's response. Our test will still pass because the response status code is still 200.

#### Modifying the test to be more robust
Here, in addition to the status code, we check if the response has the desired properties.

```js
test('if /user/:id API works', () => {
  const response = request(app).get('/user/defunkt');

  expect(response.statusCode).toBe(200);

  expect(response.login).toBeDefined();
  expect(response.id).toBeDefined();
  expect(response.node_id).toBeDefined();
  expect(response.avatar_url).toBeDefined();
  ...
})
```

### Are we done?

We have made progress. Our test case fail when a property isn't present, but we can do better. If our API returns `avatar_url: ''` our app will crash but the test will still pass.

#### Modifying the test case to make them more robust

Here we check if `avatar_url` is a string and not an empty string.

```js
test('if /user/:id API works', () => {
  const response = request(app).get('/user/defunkt');

  expect(response.statusCode).toBe(200);

  expect(response.login).toBeDefined();
  expect(response.id).toBeDefined();
  expect(response.node_id).toBeDefined();
  expect(response.avatar_url).toBeDefined();

  expect(typeof response.avatar_url).toBe('string');
  expect(response.avatar_url.length).toBeGreaterThan(0);
  ...
})
```

---

## Guidelines for writing better tests

### Code coverage is a necessary metric but not enough

In the subsequent iterations of our tests we didn't increase the code coverage. We only added more `expect` statements. Thus, a higher code coverage doesn't mean better tests.

### When a bug is found, write a test for it

Great tests can't always be written in the first iteration, but over time they can be improved. Writing a test case for a bug ensures that the bug doesn't resurface.

### Test driven development (TDD)

By the nature of TDD, it ensures that all your requirements are written down as tests. If the code fails to meet any requirement, the tests will fail.

### Use the philosophy that your test should fail

In our first and second iterations, the test case did not fail in certain cases. Add a test for everything that can go wrong. In the above example, a property could be undefined, so we added an `expect` for it.