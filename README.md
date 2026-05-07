# `@next/third-parties` pins `next@16.3.0-canary.14` in `devDependencies`, which bundles vulnerable `postcss <8.5.10` (`GHSA-qx2v-qp2m-jg93`)

### Link to the code that reproduces this issue

https://github.com/dhuang-lmg/next-third-parties-GHSA-qx2v-qp2m-jg93

### To Reproduce

### Steps to reproduce

1. Install `@next/third-parties@16.2.5` alongside `next@16.2.5`.
2. Run `npm audit`.
3. Observe 3 moderate severity findings.

```
postcss  <8.5.10
Severity: moderate
PostCSS has XSS via Unescaped </style> in CSS Stringify Output
No fix available
node_modules/next/node_modules/postcss
  next  9.3.4-canary.0 - 16.3.0-canary.5
  Depends on vulnerable versions of postcss
    @next/third-parties  *
    Depends on vulnerable versions of next
```

### Current vs. Expected behavior

### Current behavior

`@next/third-parties` pins `"next": "16.3.0-canary.14"` in its [`devDependencies`](https://github.com/vercel/next.js/blob/canary/packages/third-parties/package.json#L30). That version of `next` bundles `postcss@8.4.31` as a private nested dependency under postcss. Since `postcss@8.4.31 < 8.5.10`, `npm audit` flags a **moderate** XSS vulnerability across the entire chain.

Advisory: [GHSA-qx2v-qp2m-jg93 – PostCSS XSS via Unescaped `</style>` in CSS Stringify Output](https://github.com/advisories/GHSA-qx2v-qp2m-jg93)

### Expected behavior

`@next/third-parties` should update its pinned `devDependency` on `next` (currently `"next": "16.3.0-canary.14"` at [`packages/third-parties/package.json#L30`](https://github.com/vercel/next.js/blob/canary/packages/third-parties/package.json#L30)) to a version of `next` that bundles `postcss >= 8.5.10`, so that `npm audit` no longer flags this package.

### Provide environment information

```bash
Operating System:
  Platform: darwin
  Arch: arm64
  Version: Darwin Kernel Version 25.4.0: Thu Mar 19 19:32:59 PDT 2026; root:xnu-12377.101.15~1/RELEASE_ARM64_T8122
  Available memory (MB): 16384
  Available CPU cores: 8
Binaries:
  Node: 22.21.1
  npm: 10.9.4
  Yarn: N/A
  pnpm: N/A
Relevant Packages:
  next: 16.2.5 // Latest available version is detected (16.2.5).
  eslint-config-next: N/A
  react: 19.2.6
  react-dom: 19.2.6
  typescript: 5.9.3
Next.js Config:
  output: N/A
```

### Which area(s) are affected? (Select all that apply)

Not sure

### Which stage(s) are affected? (Select all that apply)

next dev (local), next build (local)

### Additional context

The vulnerability is introduced by `@next/third-parties` pinning `"next": "16.3.0-canary.14"` in `devDependencies` (https://github.com/vercel/next.js/blob/canary/packages/third-parties/package.json#L30).

That version of `next` bundles `postcss@8.4.31` under `node_modules/next/node_modules/postcss`. The top-level `postcss` (8.5.14) is already patched, but `npm audit` still flags the nested copy bundled inside `next`.

There is no available workaround via `npm audit fix` or package.json overrides since `postcss` is a private nested dependency of next.
