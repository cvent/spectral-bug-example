# Simple repo to demonstrate spectral bug
A Simple repo to show a bug when linting a glob pattern. 

## Installation instructions
Ensure you either have nodejs installed or asdf installed and the nodejs plugin added. If using asdf, run `asdf install`. Then run `npm install`

## Reproducing the bug
Simple scripts have been added to `package.json` to reproduce the bug. To see the bug, simply run
```
npm run lint
```
This will use spectral to lint all the .json files in this repo. This will cause the following error to be reported
```
spectral-bug-example/folder-a/schema-b.json
 1:1  error  invalid-ref  ENOENT: no such file or directory, open 'spectral-bug-example/folder-a/schema-c.json'  properties.data.properties.data.items.properties.data.items.$ref
```
But if you run any of the three following commands
```
npm run lint-a
npm run lint-b
npm run lint-c
```
which lints just a single json file at a time, no error is reported.

## Probable reasons for bug
If you look at the reported error, it is looking for `schema-c.json` in `folder-a` even though `schema-c.json` is in `folder-b`. I believe this is happening because while linting `schema-a` it fully resolves it, and because `schema-c` contains a relative reference to itself, it is preserved, and brought into `schema-a`. And when `schema-b` is resolved, it brings in the already resolved `schema-a`, including the now incorrect reference to `schema-c`.