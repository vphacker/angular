# Schematics for Bazel

## Create a Bazel-managed Angular project

To create a new Angular project that builds with Bazel, all you need to do is download the `@angular/bazel` package.

The example below assumes that there is no global installation of Angular CLI.
If you do have a global CLI installed, please skip this step.

```
$ mkdir bazel_example
$ yarn init --yes
$ yarn add --dev @angular/cli @angular/bazel
```

Create a new project using `@angular/bazel` schematics for `ng new`.

```
$ yarn ng new demo --collection=@angular/bazel --defaults
```

You should see the following set of files created.
```
CREATE demo/README.md (1021 bytes)
CREATE demo/angular.json (2380 bytes)
CREATE demo/package.json (1402 bytes)
CREATE demo/tsconfig.json (435 bytes)
CREATE demo/tslint.json (2824 bytes)
CREATE demo/.editorconfig (246 bytes)
CREATE demo/.gitignore (576 bytes)
CREATE demo/src/favicon.ico (5430 bytes)
CREATE demo/src/index.html (291 bytes)
CREATE demo/src/main.ts (372 bytes)
CREATE demo/src/polyfills.ts (3234 bytes)
CREATE demo/src/test.ts (642 bytes)
CREATE demo/src/styles.css (80 bytes)
CREATE demo/src/browserslist (388 bytes)
CREATE demo/src/karma.conf.js (980 bytes)
CREATE demo/src/tsconfig.app.json (166 bytes)
CREATE demo/src/tsconfig.spec.json (256 bytes)
CREATE demo/src/tslint.json (314 bytes)
CREATE demo/src/assets/.gitkeep (0 bytes)
CREATE demo/src/environments/environment.prod.ts (51 bytes)
CREATE demo/src/environments/environment.ts (662 bytes)
CREATE demo/src/app/app.module.ts (314 bytes)
CREATE demo/src/app/app.component.css (0 bytes)
CREATE demo/src/app/app.component.html (1120 bytes)
CREATE demo/src/app/app.component.spec.ts (972 bytes)
CREATE demo/src/app/app.component.ts (208 bytes)
CREATE demo/e2e/protractor.conf.js (752 bytes)
CREATE demo/e2e/tsconfig.e2e.json (213 bytes)
CREATE demo/e2e/src/app.e2e-spec.ts (296 bytes)
CREATE demo/e2e/src/app.po.ts (204 bytes)
CREATE demo/yarn.lock (0 bytes)
CREATE demo/BUILD.bazel (190 bytes)
CREATE demo/WORKSPACE (2951 bytes)
CREATE demo/.bazelignore (18 bytes)
CREATE demo/.bazelrc (828 bytes)
CREATE demo/e2e/BUILD.bazel (1230 bytes)
CREATE demo/e2e/protractor.on-prepare.js (1101 bytes)
CREATE demo/src/BUILD.bazel (2626 bytes)
CREATE demo/src/initialize_testbed.ts (432 bytes)
CREATE demo/src/main.dev.ts (185 bytes)
CREATE demo/src/main.prod.ts (249 bytes)
```

Note that in a Bazel-managed project, there is a Bazel WORKSPACE file and a few BUILD.bazel files.
There are also some files specific to a Bazel-managed project, namely `main.dev.ts` and `main.prod.ts`.
This is because all Angular projects built with Bazel must be AOT only.

By default, `ng new` for Bazel does not perform `yarn install`.
This is because the `node_modules` are managed by Bazel and it is Bazel's
responsibility to perform the install.

Next, we can try to build the project using Bazel.
All existing `ng` commands would work as before.

```
cd demo
yarn
yarn ng build
yarn ng serve
```

If you encounter a warning about version mismatch, update `ANGULAR_VERSION` in
the WORKSPACE file to match the version of `@angular/bazel` downloaded from NPM.

To do incremental build/test, we would need to download `@bazel/ibazel`.

```
$ yarn add --dev @bazel/ibazel
```

Bring up the dev server using `ibazel run` command.

```
yarn ibazel run //src:devserver
```

Make some changes to the code, and verify that the dev server automatically refreshes.

## Development notes

To test any local changes, run

```shell
bazel build //packages/bazel:npm_package
```

then `cd` to the npm package in the `dist` folder and run `yarn link`.
Next run `yarn link` again in the directory where the `ng` command is invoked.
Make sure the `ng` command is local, and not the global installation.

## Generate .d.ts file from JSON schema

The script to generate `.d.ts` file is located in the
[Angular CLI](https://github.com/angular/angular-cli) repo. Make sure
the CLI repository is checked out on your local machine.

Then, in the CLI repository, run the following command

```shell
bazel run //tools:quicktype_runner -- \
  ~/Documents/GitHub/angular/packages/bazel/src/schematics/ng-new/schema.json \
  ~/Documents/GitHub/angular/packages/bazel/src/schematics/ng-new/schema.d.ts
```

## TODOs

1. Make the `ts_json_schema` rule re-usable and portable.
2. Add comments in BUILD files. See discussion [here](https://github.com/angular/angular/pull/26971#discussion_r231325683).
