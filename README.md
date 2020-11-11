# socket2-libc-align-demo

As of `socket2==0.3.16`, building with Bazel hits an issue fixed by
`--cfg=libc_align`:

```
$ (cd socket2-0.3.15/ && bazel build //cargo:socket2)
Target @raze__socket2__0_3_15//:socket2 up-to-date:
  bazel-bin/external/raze__socket2__0_3_15/libsocket2--1689568412.rlib
INFO: Build completed successfully, 1 total action
```

```
$ (cd socket2-0.3.16-libc-align/ && bazel build //cargo:socket2)
Target @raze__socket2__0_3_16//:socket2 up-to-date:
  bazel-bin/external/raze__socket2__0_3_16/libsocket2--1560485693.rlib
INFO: Build completed successfully, 1 total action
```

```
$ (cd socket2-0.3.16-default/ && bazel build //cargo:socket2)
INFO: From Compiling Rust lib socket2 v0.3.16 (7 files):
error[E0063]: missing field `__align` in initializer of `libc::in6_addr`
   --> external/raze__socket2__0_3_16/src/sockaddr.rs:243:25
    |
243 |         let sin6_addr = in6_addr {
    |                         ^^^^^^^^ missing `__align`

error: aborting due to previous error

For more information about this error, try `rustc --explain E0063`.
ERROR: /HOMEDIR/.cache/bazel/_bazel_wchargin/e6b2d93af64d3456b38c9c9d2131e939/external/raze__socket2__0_3_16/BUILD.bazel:33:13: output 'external/raze__socket2__0_3_16/libsocket2--1560485693.rlib' was not created
ERROR: /HOMEDIR/.cache/bazel/_bazel_wchargin/e6b2d93af64d3456b38c9c9d2131e939/external/raze__socket2__0_3_16/BUILD.bazel:33:13: not all outputs were created or valid
Target @raze__socket2__0_3_16//:socket2 failed to build
Use --verbose_failures to see the command lines of failed build steps.
INFO: Elapsed time: 0.707s, Critical Path: 0.20s
INFO: 2 processes: 1 internal, 1 linux-sandbox.
FAILED: Build did NOT complete successfully
```

```diff
$ diff -u socket2-0.3.16-default/Cargo.toml socket2-0.3.16-libc-align/Cargo.toml
--- socket2-0.3.16-default/Cargo.toml   2020-11-11 15:00:56.977733674 -0800
+++ socket2-0.3.16-libc-align/Cargo.toml        2020-11-11 15:01:11.513593470 -0800
@@ -13,3 +13,6 @@
 workspace_path = "//cargo"
 target = "x86_64-unknown-linux-gnu"
 genmode = "Remote"
+
+[package.metadata.raze.crates.libc.'0.2.80']
+additional_flags = ["--cfg=libc_align"]
```
