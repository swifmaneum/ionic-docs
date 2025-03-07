---
previousText: '脚手架'
previousUrl: '/docs/building/scaffolding'
nextText: '跨平台'
nextUrl: '/docs/building/cross-platform'
contributors:
  - mhartington
  - kensodemann
  - elylucas
---

# 迁移指南

> 要查看Ionic4中**版本更新的完整内容**，请访问位于Ionic核心仓库中的[ 版本更新文档](https://github.com/ionic-team/ionic/blob/master/angular/BREAKING.md)

## 从Ionic3.0迁移至Ionic4.0-概览

We suggest the following general process when migrating an existing application from Ionic 3 to 4:

1. Generate a new project using the `blank` starter (see [Starting an App](/docs/building/starting))
2. Copy any Angular services from `src/providers` to `src/app/services` 
   - Services should include `{ providedIn: 'root' }` in the `@Injectable()` decorator. For details, please see Angular [provider docs](https://angular.io/guide/providers).
3. Copy the app's other root level items (pipes, components, etc) keeping in mind that the directory structure changes from `src/components` to `src/app/components`, etc.
4. Copy global Sass styling from `src/app/app.scss` to `src/global.scss`
5. Copy the rest of the application, page by page or feature by feature, keeping the following items in mind: 
   - Emulated Shadow DOM is turned on by default
   - Page/component Sass should no longer be wrapped in the page/component tag and should use Angular's [`styleUrls`](https://angular.io/api/core/Component#styleUrls) option of the `@Component` decorator
   - RxJS has been updated from v5 to v6 (see [RxJS Changes](#rxjs-changes))
   - Certain lifecycle hooks should be replaced by Angular's hooks (see [Lifecycle Events](#lifecycle-events))
   - Markup changes may be required (migration tool available, see [Markup Changes](#markup-changes))

In many cases, using the Ionic CLI to generate a new object and then copying the code also works very well. For example: `ionic g service weather` will create a shell `Weather` service and test. The code can then be copied from the older project with minor modifications as needed. This helps to ensure proper structure is followed. This also generates shells for unit tests.

## Changes in Package Name

In Ionic 4, the package name is `@ionic/angular`. Uninstall Ionic 3 and install Ionic 4 using the new package name:

```shell
$ npm uninstall ionic-angular
$ npm install @ionic/angular
```

While migrating an app, update the imports from `ionic-angular` to `@ionic/angular`.

## Project structure

One of the major changes between an Ionic 3 app and an Ionic 4 app is the overall project layout and structure. In v3, Ionic apps had a custom convention for how an app should be setup and what that folder structure should look like. In v4, this has been changed to follow the recommended setup of each supported framework.

For example, if an app is using Angular, that project structure will be exactly what an Angular CLI app would be. This change, while not too difficult to accommodate, helps to keep common patterns and documentation consistent.

<ion-grid> <ion-row> <ion-col> <figure> <file-tree> <file-tree-directory name="src"> <file-tree-directory name="app"> <file-tree-directory name="about" collapsed></file-tree-directory> <file-tree-directory name="home" collapsed></file-tree-directory> <file-tree-file name="app-routing.module.ts"></file-tree-file> <file-tree-file name="app.component.html"></file-tree-file> <file-tree-file name="app.component.spec.ts"></file-tree-file> <file-tree-file name="app.component.ts"></file-tree-file> <file-tree-file name="app.module.ts"></file-tree-file> </file-tree-directory> <file-tree-directory name="assets" collapsed></file-tree-directory> <file-tree-directory name="environments" collapsed></file-tree-directory> <file-tree-directory name="theme" collapsed></file-tree-directory> <file-tree-file name="global.scss"></file-tree-file> <file-tree-file name="index.html"></file-tree-file> <file-tree-file name="karma.conf.js"></file-tree-file> <file-tree-file name="main.ts"></file-tree-file> <file-tree-file name="polyfills.ts"></file-tree-file> <file-tree-file name="test.ts"></file-tree-file> <file-tree-file name="tsconfig.app.json"></file-tree-file> <file-tree-file name="tsconfig.spec.json"></file-tree-file> </file-tree-directory> <file-tree-file name=".gitignore"></file-tree-file> <file-tree-file name="angular.json"></file-tree-file> <file-tree-file name="ionic.config.json"></file-tree-file> <file-tree-file name="package.json"></file-tree-file> <file-tree-file name="tsconfig.json"></file-tree-file> <file-tree-file name="tslint.json"></file-tree-file> </file-tree> <figcaption>Ionic 4</figcaption> </figure> </ion-col> <ion-col> <figure> <file-tree> <file-tree-directory name="src"> <file-tree-directory name="app"> <file-tree-file name="app.component.html"></file-tree-file> <file-tree-file name="app.html"></file-tree-file> <file-tree-file name="app.module.ts"></file-tree-file> <file-tree-file name="app.scss"></file-tree-file> <file-tree-file name="main.ts"></file-tree-file> </file-tree-directory> <file-tree-directory name="assets" collapsed></file-tree-directory> <file-tree-directory name="pages"> <file-tree-directory name="about" collapsed></file-tree-directory> <file-tree-directory name="home" collapsed></file-tree-directory> </file-tree-directory> <file-tree-directory name="theme" collapsed></file-tree-directory> <file-tree-file name="index.html"></file-tree-file> <file-tree-file name="manifest.json"></file-tree-file> <file-tree-file name="service-worker.js"></file-tree-file> </file-tree-directory> <file-tree-file name=".gitignore"></file-tree-file> <file-tree-file name="ionic.config.json"></file-tree-file> <file-tree-file name="package.json"></file-tree-file> <file-tree-file name="tsconfig.json"></file-tree-file> <file-tree-file name="tslint.json"></file-tree-file> </file-tree> <figcaption>Ionic 3</figcaption> </figure> </ion-col> </ion-row> </ion-grid>

The above comparison is an example of a v4 app's project structure. For developers with experience in a vanilla Angular project, this should feel really familiar.

There is a `src/` directory that acts as the home for the app. This includes the `index.html`, any assets, environment configuration, and any app specific config files.

While migrating an app to take advantage of this new layout, it is suggested that a new project "base" is made with the CLI. Then, with the new project layout, migrate the features of the app piece by piece. Pages/components/etc. should be moved into the `src/app/` folder.

Ensure your Ionic configuration file has the appropriate `type`. The project type for v3 is `ionic-angular`. The project type for v4 is `angular`. If this value is incorrect, the CLI may invoke the incorrect build scripts.

See the following `ionic.config.json` as an example:

```json
{
  "name": "my-app",
  "type": "angular"
}
```

## RxJS Changes

Between V3 and V4, RxJS was updated to version 6. This changes many of the import paths of operators and core RxJS functions. Please see the <a href="https://github.com/ReactiveX/rxjs/blob/master/docs_app/content/guide/v6/migration.md" target="_blank">RxJS Migration Guide</a> for details.

## Lifecycle Events

With V4, we're now able to utilize the typical events provided by [Angular](https://angular.io/guide/lifecycle-hooks). But for certain cases, you might want to have access to the events fired when a component has finished animating during it's route change. In this case, the `ionViewWillEnter`, `ionViewDidEnter`, `ionViewWillLeave`, and `ionViewDidLeave` have been ported over from V3. Use these events to coordinate actions with Ionic's own animations system.

Older events like `ionViewDidLoad`, `ionViewCanLeave`, and `ionViewCanEnter` have been removed, and the proper Angular alternatives should be used.

For more details, checkout the [router-outlet docs](/docs/api/router-outlet)

## Overlay Components

In prior versions of Ionic, overlay components such as Loading, Toast, or Alert were created synchronously. In Ionic v4, these components are all created asynchronously. As a result of this, the API is now promise-based.

```typescript
// v3
showAlert() {
  const alert = this.alertCtrl.create({
    message: "Hello There",
    subHeader: "I'm a subheader"
  });

  alert.present();
}
```

In v4, promises are used:

```typescript
showAlert() {
  this.alertCtrl.create({
    message: "Hello There",
    subHeader: "I'm a subheader"
  }).then(alert => alert.present());
}

// Or using async/await

async showAlert() {
  const alert = await this.alertCtrl.create({
    message: "Hello There",
    subHeader: "I'm a subheader"
  });

  await alert.present();
}
```

## Navigation

In V4, navigation received the most changes. Now, instead of using Ionic's own `NavController`, we integrate with the official Angular Router. This not only provides a consistent routing experience across apps, but is much more dependable. The Angular team has an <a href="http://angular.io/guide/router" target="_blank">excellent guide</a> on their docs site that covers the Router in great detail.

To provide the platform specific animations that users are used to, we have created `ion-router-outlet` for Angular Apps. This behaves in a similar manner to Angular's `router-outlet` but provides a stack-based navigation (tabs) and animations.

For a detailed explanation in navigation works in a V4 project, checkout the [Angular navigation guide](/docs/angular/navigation).

## Lazy Loading

Since Navigation has changed, the mechanism for lazy loading has also changed in V4.

In v3, a typical lazy loading setup worked like this:

```typescript
// home.page.ts
@IonicPage({
  segment: 'home'
})
@Component({ ... })
export class HomePage {}

// home.module.ts
@NgModule({
  declarations: [HomePage],
  imports: [IonicPageModule.forChild(HomePage)]
})
export class HomePageModule {}
```

However, in v4, lazy loading is done via the `loadChildren` method of the Angular router:

```typescript
// home.module.ts
@NgModule({
  imports: [
    IonicModule,
    RouterModule.forChild([{ path: '', component: HomePage }])
  ],
  declarations: [HomePage]
})
export class HomePageModule {}

// app.module.ts
@NgModule({
  declarations: [AppComponent],
  imports: [
    BrowserModule,
    IonicModule.forRoot(),
    RouterModule.forRoot([
      { path: 'home', loadChildren: './pages/home/home.module#HomePageModule' },
      { path: '', redirectTo: 'home', pathMatch: 'full' }
    ])
  ],
  bootstrap: [AppComponent]
})
export class AppModule {}
```

For a detailed explanation of lazy loading in V4 project, check out the [Angular navigation guide](docs/navigation/angular#lazy-loading-routes).

## Markup Changes

Since v4 moved to Custom Elements, there's been a significant change to the markup for each component. These changes have all been made to follow the Custom Elements spec, and have been documented in a <a href="https://github.com/ionic-team/ionic/blob/master/angular/BREAKING.md#breaking-changes" target="_blank">dedicated file on GitHub</a>.

To help with these markup changes, we've released a TSLint-based <a href="https://github.com/ionic-team/v4-migration-tslint" target="_blank">Migration Tool</a>, which detects issues and can even fix some of them automatically.

## Migrating from Ionic 1.0 to Ionic 4.0 - Overview

### Ionic 1.0 to Ionic 4.0: What’s Involved?

Migrating from Ionic 1 to Ionic 4.0 involves moving from AngularJS (aka Angular 1) to Angular 7+. There are many architectural differences between these versions, so some of the app code will have to be rewritten. The amount of work involved depends on the complexity and size of your app.

One upside is that for the most part, the Ionic UI components you know and love from V1 haven’t changed much.

Here are some considerations to review before beginning the upgrade:

- **App complexity**: Naturally, the larger and more complex the app is, the longer it will take to migrate.
- **Framework support**: In 2019, Ionic will release full support for React and Vue. You can also use Ionic Framework components [without a framework](/docs/installation/cdn/). Since these are not production ready yet, we recommend sticking with Angular or waiting until the other framework support is available.
- **Budget and team makeup**: The length of a migration project will vary based on the size of your team, the complexity of the app, and the amount of time allotted to make the transition.

### Suggested Strategy

Once your development team has identified a good time frame for beginning the migration, Ionic recommends feature-freezing the Ionic 1 application and getting the code in order: Fix any major bugs, eliminate tech debt, and reorganize as you see fit. Then, identify which features to migrate over and which to abandon.

Once the Ionic 1 app is stable, create a new Ionic 4.0 project. The majority of the dev team’s attention should be given to the new project; only bugs should be fixed in the Ionic 1 app to ensure that the transition happens as quickly and smoothly as possible.

Once the team is comfortable that the Ionic 4.0 app has become stable and has fulfilled a core set of features, you can then shut down the Ionic 1 app.

### Moving From AngularJS to Angular

Please reference official [Angular upgrade guide](https://angular.io/guide/upgrade) information.

### Ionic Changes

Our Ionic 3.0 to Ionic 4.0 migration sections above may prove to be a useful reference. Generate a new Ionic 4.0 project using the blank starter (see [Starting an App](/docs/building/starting)). Spend time getting familiar with Ionic 4.0 components. Happy building!

### Need Assistance?

If your team would like assistance with the migration, please [reach out to us](https://ionicframework.com/enterprise-engine)! Ionic offers Advisory Services, which includes Ionic 4.0 training, architecture reviews, and migration assistance.