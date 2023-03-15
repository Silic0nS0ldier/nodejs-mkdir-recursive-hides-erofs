# `mkdir` in Recursive Mode on a Readonly Filesystem Repro

Reproduction of issue with the `recursive` option in `mkdir` where `EROFS` errors are hidden by the catch-all result of a `stat` call.

Tags shift over time, for consistency an image digest is used.
```sh
# docker image pull node:19-alpine3.16
# 19-alpine3.16: Pulling from library/node
# Digest: sha256:41ef4e42ac20476e71cd49a661d34282eaa40937cadac24da951d986b7544ca3
# ...
```

```sh
# Expect: EROFS error
# Actual: ENOENT error
docker run --rm -it -v $(pwd):/testing:ro node@sha256:41ef4e42ac20476e71cd49a661d34282eaa40937cadac24da951d986b7544ca3 node -e 'require("fs").mkdirSync("/testing/path/does/not/exist", { recusive: true })'
# Expect: Complete without error
# Actual: Complete without error
docker run --rm -it -v $(pwd):/testing:ro node@sha256:41ef4e42ac20476e71cd49a661d34282eaa40937cadac24da951d986b7544ca3 node -e 'require("fs").mkdirSync("/testing/path/exists", { recursive: true })'
# Expect: EROFS error
# Actual: EROFS error
docker run --rm -it -v $(pwd):/testing:ro node@sha256:41ef4e42ac20476e71cd49a661d34282eaa40937cadac24da951d986b7544ca3 node -e 'require("fs").mkdirSync("/testing/path/does-not-exist")'
# Expect: EEXIST error
# Actual: EEXIST error
docker run --rm -it -v $(pwd):/testing:ro node@sha256:41ef4e42ac20476e71cd49a661d34282eaa40937cadac24da951d986b7544ca3 node -e 'require("fs").mkdirSync("/testing/path/exists")'
```
