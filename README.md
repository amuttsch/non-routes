The change [#145](https://github.com/netlify/next-on-netlify/pull/145) "revert route/redirect sorting logic to static then dynamic" causes the `_redirects` file to be sorted wrong when using an optional catch all route and dynamic paths. 

## How to reproduce

Execute the following command

```bash
yarn install
yarn netlify
```

### Problem

The resulting `_redirects` file looks like this on version `^2.8.3`

```
# Next-on-Netlify Redirects
/_next/data/tveFS5JTA64mmnqiHIWfA/test.json  /.netlify/functions/next_test  200
/_next/data/tveFS5JTA64mmnqiHIWfA/index.json  /.netlify/functions/next_any  200
/_next/data/tveFS5JTA64mmnqiHIWfA/*  /.netlify/functions/next_any  200
/api/hello  /.netlify/functions/next_api_hello  200
/test  /.netlify/functions/next_test  200
/_next/data/tveFS5JTA64mmnqiHIWfA/:bar/test.json  /.netlify/functions/next_bar_test  200
/_next/image*  url=:url w=:width q=:quality  /.netlify/functions/next_image?url=:url&w=:width&q=:quality  200
/:bar/test  /.netlify/functions/next_bar_test  200
/  /.netlify/functions/next_any  200
/_next/*  /_next/:splat  200
/*  /.netlify/functions/next_any  200
```

### Expected

Previous to `2.8.3` the file looks like this:

```
# Next-on-Netlify Redirects
/_next/data/xcFf_YKEf4dQzabGKlRTV/test.json  /.netlify/functions/next_test  200
/_next/data/xcFf_YKEf4dQzabGKlRTV/:bar/test.json  /.netlify/functions/next_bar_test  200
/_next/data/xcFf_YKEf4dQzabGKlRTV/index.json  /.netlify/functions/next_any  200
/_next/data/xcFf_YKEf4dQzabGKlRTV/*  /.netlify/functions/next_any  200
/_next/image*  url=:url w=:width q=:quality  /.netlify/functions/next_image?url=:url&w=:width&q=:quality  200
/api/hello  /.netlify/functions/next_api_hello  200
/test  /.netlify/functions/next_test  200
/:bar/test  /.netlify/functions/next_bar_test  200
/  /.netlify/functions/next_any  200
/_next/*  /_next/:splat  200
/*  /.netlify/functions/next_any  200
```


## Problem

The data routes `/_next/data` are split into two groups. `/_next/data/tveFS5JTA64mmnqiHIWfA/*  /.netlify/functions/next_any  200` catches any data route, but `/_next/data/tveFS5JTA64mmnqiHIWfA/:bar/test.json  /.netlify/functions/next_bar_test  200` is below this redirect. This causes our application to fail to load data when the user does a client side transition.

Workaround: Downgrade `next-on-netlify` to `2.8.2`.
