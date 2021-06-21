# Nested Secrets in GitHub Actions

This is useful if you want to only store 1 secret with multiple parts, for example:

Create a secret in GitHub called **CREDENTIALS**

```
{
  "username": "some clever username",
  "password": "some secure password"
}
```

Reference it in the action:

```
USERNAME=$(echo '${{ secrets.credentials }}' | jq -r '.username')
```

When run, your action will output something like this:

```
USERNAME=$(echo '***
***
***' | jq -r '.username')
```

Normally when using secrets, GitHub will automatically mask the output, as you can see in the example above, the whole JSON blob is replaced with `***`

However if you have a line to output part of the secret, this won't happen, e.g:

```
echo "$USERNAME" # => some clever username
```

You will notice that the output **isn't** masked in the Actions log, which could potentially expose your secrets to the world.

So you will want to tell the GitHub actions runner that this (sub)string is also a secret on it's own by using the `::add-mask` option

```
echo "$USERNAME" # => some clever username
echo "::add-mask::$USERNAME"
echo "$USERNAME" # => ***
```
