# Scaffold Angular Application with Advanced Tooling

[angular-cli](https://angular.io/cli) provides an amazing tooling for scaffolding, building, testing and deploying Angular applications. However, when you want to perform additional tasks like the following, the tooling is not enough:

- Linting SCSS, HTML
- Format with Prettier
- Commit message linting
- Pre-commit hooks
- Mock server
- Translations
- Configuration management
- Logging

In this article, we will see how to add these features to an Angular application created with angular-cli.

## Prerequisites

- [Node.js](https://nodejs.org/en/download/)
- [Yarn](https://classic.yarnpkg.com/en/docs)
- [Angular CLI](https://angular.io/cli)

> At the time of this notes, angular was 17x, node LTS was 20x and yarn was 1.22x.

## Scaffold base angular

Generate the base Angular application with the following command:

```
ng new [my-app] --package-manager=yarn --style=scss --routing=true --ssr=false
```

??? info "Read more"
    More about this command in the [official documentation](https://angular.io/cli/new).

## Angular ESLint

Add ESLint dependency to the project with the following command:

```
ng add @angular-eslint/schematics
```

??? info "Read more"
    More about **`angular-eslint`** in the [official documentation](https://github.com/angular-eslint/angular-eslint).

## Lint SCSS

Add SCSS linting dependencies to the project with the following command:

```
yarn add -D stylelint stylelint-config-sass-guidelines stylelint-scss
```

Add the configuration in `.lint/.stylelintrc.json`:

```json title=".lint/.stylelintrc.json"
{
  "extends": "stylelint-config-sass-guidelines",
  "plugins": [
    "stylelint-scss"
  ],
  "rules": {
    "color-hex-length": "long",
    "selector-pseudo-element-no-unknown": [
      true,
      {
        "ignorePseudoElements": [
          "ng-deep"
        ]
      }
    ]
  }
}
```

Add the script in `package.json`:

```json title="package.json"
{
  "scripts": {
    "lint:styles": "stylelint --config .lint/.stylelintrc.json src/app/**/*.scss"
  }
}
```

??? info "Read more"
    More about **`stylelint`** in the [official documentation](https://stylelint.io/awesome-stylelint/).

## Lint HTML

Add HTML linting dependency to the project with the following command:

```
yarn add -D htmlhint
```

Add the configuration in `.lint/.htmlhintrc`:

```json title=".lint/.htmlhintrc"
{
  "tagname-lowercase": true,
  "attr-lowercase": true,
  "attr-value-double-quotes": true,
  "attr-value-not-empty": false,
  "attr-no-duplication": true,
  "doctype-first": false,
  "tag-pair": true,
  "tag-self-close": false,
  "spec-char-escape": true,
  "id-unique": true,
  "src-not-empty": true,
  "title-require": true,
  "alt-require": true,
  "doctype-html5": true,
  "id-class-value": "dash",
  "style-disabled": true,
  "inline-style-disabled": true,
  "inline-script-disabled": true,
  "space-tab-mixed-disabled": "space",
  "id-class-ad-disabled": true,
  "href-abs-or-rel": false,
  "attr-unsafe-chars": true
}
```

Add the script in `package.json`:

```json title="package.json"
{
  "scripts": {
    "lint:html": "htmlhint --config .lint/.htmlhintrc src/app/**/*.html"
  }
}
```

??? info "Read more"
    More about **`htmlhint`** in the [official documentation](https://htmlhint.com/docs/user-guide/getting-started).

## Update ng lint script

```diff title="package.json"
{
  "scripts": {
-   "lint": "ng lint"
+   "lint:ng": "ng lint"
  }
}
```

## Run all linters

In order to run all linters we need a dependency npm-run-all. Add the following dependency to the project with the following command:

```shell
yarn add -D npm-run-all
```

Add the script in `package.json`:

```json title="package.json"
{
  "scripts": {
    "lint": "npm-p lint:ng lint:styles lint:html"
  }
}
```

??? info "Read more"
    More about **`npm-run-all`** in the [official documentation](https://github.com/mysticatea/npm-run-all/tree/master/docs).

## Format with Prettier

Add Prettier dependencies to the project with the following command:

```
yarn add -D prettier
```

Add the configuration in `.prettierrc`:

```json title=".prettierrc"
{
  "printWidth": 120,
  "tabWidth": 2,
  "useTabs": false,
  "semi": true,
  "singleQuote": true,
  "bracketSpacing": true
}
```

If you wish to ignore some files, add the configuration in `.prettierignore`:

``` title=".prettierignore"
package.json
package-lock.json
yarn
yarn.error.log
```

??? info "Read more"
    More about **`Prettier`** in the [official documentation](https://prettier.io/docs/en/install).

## Format staged files

Add lint-staged dependency to the project with the following command:

```
yarn add -D lint-staged
```

Add the configuration in `.lintstagedrc.json`:

```json title=".lintstagedrc.json"
{
  "**/*.{ts,js}": "eslint --cache --fix",
  "*": "prettier --cache --ignore-unknown --write"
}
```

For formatting and linting together add the script in `package.json`:

```json title="package.json"
{
  "scripts": {
    "format:prettier": "lint-staged",
    "format:all": "run-s format:prettier lint"
  }
}
```

??? info "Read more"
    More about **`lint-staged`** in the [official documentation](https://github.com/lint-staged/lint-staged#readme)

## Commit message linting

Add commitlint dependencies to the project with the following command:

```
yarn add -D @commitlint/{config-conventional,cli}
```

Add the configuration in `commitlint.config.js`:

```javascript title="commitlint.config.js" 
module.exports = {extends: ['@commitlint/config-conventional']};
```

??? info "Read more"
    More about **`commitlint`** in the [official documentation](https://commitlint.js.org/guides/getting-started.html).

## Pre-commit hooks

Add husky dependency to the project with the following command:

```
yarn add -D husky
```

Initialize husky with the following command:

```
npx husky init
```

Update pre-commit hooks with the following command:

```
echo "yarn format:all" > .husky/pre-commit 
```

Update pre-push hooks with the following command:

```
echo "yarn test" > .husky/pre-push 
```

Update commit-msg hooks with the following command:

```
echo "yarn commitlint --edit \$1" > .husky/commit-msg
```

??? info "Read more"
    More about **`husky`** in the [official documentation](https://typicode.github.io/husky/).

## Mock server

In this section you will basically create a mock server using json-server for the API requests and proxy the requests to the mock server.

Add json-server dependency to the project with the following command:

```
yarn add -D json-server
```

Update the script in `package.json`:

```json title="package.json"
{
  "scripts": {
    "mock:server": "json-server --watch mock/db.json --port 3000",
    "start": "run-p mock:server \"ng serve\""
  }
}
```

??? info "Read more"
    More about **`json-server`** in the [official documentation](https://github.com/typicode/json-server/tree/v0?tab=readme-ov-file#table-of-contents).

You might also want to proxy the API requests to the mock server. Add the following configuration in `proxy.conf.json`:

```json title="proxy.conf.json"
{
  "/api": {
    "target": "http://localhost:3000",
    "secure": false
  }
}
```

In the angular.json file, update the serve options to use the proxy configuration:

```diff title="angular.json"
{
  "projects": {
    "[my-app]": {
      "architect": {
        "serve": {
          "options": {
+           "proxyConfig": "proxy.conf.json"
          }
        }
      }
    }
  }
}
```

??? info "Read more"
    More about this configuration in the [official documentation](https://angular.io/guide/build#proxying-to-a-backend-server).

## Translations

Add ngx-translate dependencies to the project with the following command:

```
yarn add @ngx-translate/core @ngx-translate/http-loader
```

We are using `ngx-translate/http-loader` to load translations from a JSON file. Create a `src/assets/i18n/en.json` file with the following content:

```json title="src/assets/i18n/en.json"
{
  "HELLO": "Hello World!"
}
```

Update app.config.ts file with the following content:

```typescript title="src/app/app.config.ts"
import { APP_INITIALIZER, ApplicationConfig, importProvidersFrom, Injector } from '@angular/core';
import { provideRouter } from '@angular/router';

import { routes } from './app.routes';
import { TranslateHttpLoader } from '@ngx-translate/http-loader';
import { HttpClient, HttpClientModule } from '@angular/common/http';
import { TranslateLoader, TranslateModule, TranslateService } from '@ngx-translate/core';
import { appInitializerFactory } from './appInitializerFactory';

const httpLoaderFactory = (http: HttpClient): TranslateLoader => new TranslateHttpLoader(http);

export const appConfig: ApplicationConfig = {
  providers: [
    provideRouter(routes),
    importProvidersFrom(HttpClientModule),
    importProvidersFrom(
      TranslateModule.forRoot({
        loader: {
          provide: TranslateLoader,
          useFactory: httpLoaderFactory,
          deps: [HttpClient],
        },
      }),
    ),
    {
      provide: APP_INITIALIZER,
      useFactory: appInitializerFactory,
      deps: [TranslateService, Injector],
      multi: true,
    },
  ],
};
```

Create appInitializerFactory.ts file with the following content:

```typescript title="src/app/appInitializerFactory.ts"
import {Injector} from '@angular/core';
import {TranslateService} from '@ngx-translate/core';
import {LOCATION_INITIALIZED} from '@angular/common';

export const appInitializerFactory = (translate: TranslateService, injector: Injector) => () =>
    new Promise((resolve) => {
      const locationInitialized = injector.get(LOCATION_INITIALIZED, Promise.resolve(null));
      locationInitialized.then(() => {
        const langToSet = 'en'; // todo: this can be injected from a user service to get the user's preferred language
        translate.setDefaultLang('en'); //todo: this can be injected from env variable
        translate.use(langToSet).subscribe({
          next: () => {
            console.log(`Successfully initialized '${langToSet}' language.`); // todo: eplace this by a logger
          },
          error: (err) => {
            console.error(`Problem with '${langToSet}' language initialization.`, err);
          },
          complete: () => {
            resolve(null);
          },
        });
      });
    });
```

??? info "Read more"
    More about **`ngx-translate`** in the [official documentation](https://github.com/ngx-translate/core?tab=readme-ov-file#ngx-translatecore--).

## Configuration management

You may want to manage configuration of your application through environment variables. Add the  @ngx-env dependency to the project with the following command:

```
ng add @ngx-env/builder
```

The above command will add dev dependency of @ngx-env/builder and update the angular.json file to use the builder. You can then create a `.env` file with the following content:

```shell title=".env"
NG_APP_DEFAULT_LANGUAGE=en
```

Since we are using typescript, we can strictly type the environment variables with the new file created by the ngx-env/build i.e. src/app/env.d.ts:

```typescript title="src/app/env.d.ts"
interface ImportMeta {
  readonly env: ImportMetaEnv;
}

interface ImportMetaEnv {
  /**
   * Built-in environment variable.
   * @see Docs https://github.com/chihab/dotenv-run/packages/angular#node_env.
   */
  readonly NODE_ENV: string;
  readonly NG_APP_DEFAULT_LANGUAGE: string;
  // Add your environment variables below
  // readonly NG_APP_API_URL: string;
  [key: string]: any;
}
```

Now you should be able to access the environment variables in your application like so:

```diff title="src/app/appInitializerFactory.ts"
import { Injector } from '@angular/core';
import { TranslateService } from '@ngx-translate/core';
import { LOCATION_INITIALIZED } from '@angular/common';

export const appInitializerFactory = (translate: TranslateService, injector: Injector) => () =>
  new Promise((resolve) => {
    const locationInitialized = injector.get(LOCATION_INITIALIZED, Promise.resolve(null));
    locationInitialized.then(() => {
      const langToSet = 'en'; // todo: this can be injected from a user service to get the user's preferred language
+     translate.setDefaultLang(import.meta.env.NG_APP_DEFAULT_LANGUAGE);
      translate.use(langToSet).subscribe({
        next: () => {
          console.log(`Successfully initialized '${langToSet}' language.`);
        },
        error: (err) => {
          console.error(`Problem with '${langToSet}' language initialization.`, err);
        },
        complete: () => {
          resolve(null);
        },
      });
    });
  });
```

??? info "Read more"
    More about **`ngx-env`** in the [official documentation](https://github.com/chihab/dotenv-run/blob/main/README.md#ngx-envbuilder).

You may also want to choose when .env file you would like to load, and for doing that you can add cross-env dependency to the project with the following command:

```
yarn add -D cross-env
```

Update the start script in `package.json`:

```json title="package.json"
{
  "scripts": {
    "start": "cross-env NODE_ENV=local run-p mock:server \"ng serve\""
  }
}
```

??? info "Read more"
    More about **`cross-env`** in the [official documentation](https://github.com/kentcdodds/cross-env?tab=readme-ov-file#cross-env-).

## Logging

Add ngx-logger dependencies to the project with the following command:

```
yarn add ngx-logger
```

Update app.config.ts file with the following content:

```diff title="src/app/app.config.ts"
import { APP_INITIALIZER, ApplicationConfig, importProvidersFrom, Injector } from '@angular/core';
import { provideRouter } from '@angular/router';

import { routes } from './app.routes';
import { TranslateHttpLoader } from '@ngx-translate/http-loader';
import { HttpClient, HttpClientModule } from '@angular/common/http';
import { TranslateLoader, TranslateModule, TranslateService } from '@ngx-translate/core';
import { appInitializerFactory } from './appInitializerFactory';
+ import { LoggerModule, NGXLogger, NgxLoggerLevel } from 'ngx-logger';

const httpLoaderFactory = (http: HttpClient): TranslateLoader => new TranslateHttpLoader(http);

export const appConfig: ApplicationConfig = {
  providers: [
    provideRouter(routes),
    importProvidersFrom(HttpClientModule),
    importProvidersFrom(
      TranslateModule.forRoot({
        loader: {
          provide: TranslateLoader,
          useFactory: httpLoaderFactory,
          deps: [HttpClient],
        },
      }),
    ),
    {
      provide: APP_INITIALIZER,
      useFactory: appInitializerFactory,
+     deps: [TranslateService, Injector, NGXLogger],
      multi: true,
    },
+   importProvidersFrom(LoggerModule.forRoot( { level: NgxLoggerLevel.DEBUG}))
  ],
};
```

Update appInitializerFactory.ts file with the following content:

```diff title="src/app/appInitializerFactory.ts"
import { Injector } from '@angular/core';
import { TranslateService } from '@ngx-translate/core';
import { LOCATION_INITIALIZED } from '@angular/common';
+ import { NGXLogger } from 'ngx-logger';

export const appInitializerFactory = (translate: TranslateService, injector: Injector, logger: NGXLogger) => () =>
  new Promise((resolve) => {
    const locationInitialized = injector.get(LOCATION_INITIALIZED, Promise.resolve(null));
    locationInitialized.then(() => {
      const langToSet = 'en'; // todo: this can be injected from a user service to get the user's preferred language
      translate.setDefaultLang(import.meta.env.NG_APP_DEFAULT_LANGUAGE);
      translate.use(langToSet).subscribe({
        next: () => {
+         logger.info(`Successfully initialized '${langToSet}' language.`);
        },
        error: (err) => {
+         logger.error(`Problem with '${langToSet}' language initialization.`, err);
        },
        complete: () => {
          resolve(null);
        },
      });
    });
  });
```

??? info "Read more"
    More about **`ngx-logger`** in the [official documentation](https://github.com/dbfannin/ngx-logger?tab=readme-ov-file#ngx-logger).
