## Turborepo issue reproduction

This repo reproduces a Turborepo regression where `peerDependencies` create unexpected
workspace build edges.

The shape is:

- `a` depends on local workspace `b`
- `b` has a concrete dependency on published `buffer@6.0.3`
- `b` also has a `peerDependency` on local workspace `buffer`
- local `buffer` then pulls in local `d` and `e`

With the broken Turbo version, building `a` incorrectly walks the peer edge and builds:

- `buffer`
- `d`
- `e`

even though `a` only depends on `b`, and `b`'s concrete `buffer` dependency is the
published npm package.

### Steps to reproduce

1. Clone this repo
2. Run `yarn` in the root of the repo
3. Run `yarn build:turbo`

Expected behavior:

- Only `b` and `a` should build

Broken behavior on Turbo `2.8.11`+:

- `buffer`, `d`, and `e` also build

The regression window in the real repo is:

- good: `2.8.11-canary.1`
- bad: `2.8.11-canary.2`

I believe the cause is this line, https://github.com/vercel/turborepo/commit/9deb87b798cc02490820fa2e2d5e80a733d644e2#diff-33c89286f9ef56960c175369a939b24370968f01000cc5db41b7c28a678680beR210
