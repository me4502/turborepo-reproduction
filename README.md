## Turborepo issue reproduction

This repo is a reproduction for a Turborepo issue, where it incorrectly builds workspaces when an npm package of the same name is used in a workspace. While the setup shown in the reproduction is fairly silly, this is mostly for simplicity of demonstration. This is mostly relevant in repos that have FFI packages that are built in specialised CI environments and always accessed via npm rather than from source.

The actual bug here is present in workspace `a`, where the dependency of `buffer`, using the `npm:` protocol from Yarn, inadvertently triggers a build of the other workspace titled `buffer`, despite that workspace _not_ being a dependency of it.

### Steps to reproduce

1. Clone this repo
2. Run `yarn` in the root of the repo
3. `cd` to `workspaces/a`
4. Run `yarn build:turbo`

You'll note that the following output will display,

```
turbo 2.0.12

• Packages in scope: a
• Running build in 1 packages
• Remote caching disabled
buffer:build: cache miss, executing 1b2dac6fa4b10094
buffer:build:
buffer:build: built buffer
a:build: cache miss, executing 6244eed3a659492f
a:build:
a:build: built a

 Tasks:    2 successful, 2 total
Cached:    0 cached, 2 total
  Time:    456ms
```

As can be seen, the buffer workspace's `build` script has been incorrectly run, despite it not being a dependency of the `a` workspace.
