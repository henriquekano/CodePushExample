# CodePushExample

An example in implementing Code push on a new react native project

## Creating a account
Go here https://microsoft.github.io/code-push/

## Login in the terminal
`appcenter login`

## Create an app (on the platform, not a mobile app...)
For each platform (ios, android, windows - lol), you must create a separate app:
https://appcenter.ms/users/{username}/applications/create

## App (now's the mobile app)
install dependencies:
```bash
$ yarn add react-native-code-push
$ react-native link react-native-code-push
```

The process should ask for the deployment key. Run `appcenter codepush deployment list -a {username}/{appname} --displayKeys` and pick a key.
Use the staging key if you are still unsure of life.

**Do remember that you created TWO apps - one for each platform, so remember to provide the correct app's key for each platform. Mada facka**

If you want to set these keys above manually, first, what's your problem? Second:
 - on android: search `new CodePush`, it's the first parameter
 - on ios: Info.plist: `CodePushDeploymentKey` on the root

install the CLI:
```
yarn global add appcenter-cli
```

great.

Wrap the root of your app with code push's HoC (the one being passed to `AppRegistry.registerComponent`):
```js
import codePush from 'react-native-code-push';

const codePushConfig = {
  checkFrequency: codePush.CheckFrequency.ON_APP_RESUME,
  installMode: codePush.InstallMode.ON_NEXT_RESUME
}

export default codePush(codePushConfig)(App)
```

Very nice.

## Release

Now, run the app in 'release mode':
```bash
ios: $ react-native run-ios --configuration Release
android: $ react-native run-android --variant=release
```
This may vary depending on the project (search android's variants/flavors and ios something?)

Change some code, and freaking deploy this sheit:
```bash
appcenter codepush release-react -a {username}/{appname}
```

Congratulations, nerd. You did it. Are you happy with yourself? Were you able to fill the void inside? No? Great!

If something went wrongly, I don't care. Don't @ me.

## Nerd stuff

If you are paranoid:
```
## Store Guideline Compliance

(https://docs.microsoft.com/en-us/appcenter/distribution/codepush/react-native#store-guideline-compliance)

While Google Play and internally distributed apps (for example Enterprise, Fabric, HockeyApp) have no limitations over how to publish updates using CodePush, the iOS App Store and its corresponding guidelines have more precise rules you should be aware of before integrating the solution within your application.

Paragraph 3.3.2, since back in 2015's Apple Developer Program License Agreement fully allowed performing over-the-air updates of JavaScript and assets - and in its latest version (20170605) downloadable here this ruling is even broader:

> Interpreted code may be downloaded to an Application but only so long as such code: (a) does not change the primary purpose of the Application by providing features or functionality that are inconsistent with the intended and advertised purpose of the Application as submitted to the App Store, (b) does not create a store or storefront for other code or applications, and (c) does not bypass signing, sandbox, or other security features of the OS.

CodePush allows you to follow these rules in full compliance so long as the update you push does not significantly deviate your product from its original App Store approved intent.

To further remain in compliance with Apple's guidelines we suggest that App Store-distributed apps don't enable the updateDialog option when calling sync, since in the App Store Review Guidelines it is written that:

> Apps must not force users to rate the app, review the app, download other apps, or other similar actions in order to access functionality, content, or use of the app.

This is not necessarily the case for updateDialog, since it won't force the user to download the new version, but at least you should be aware of that ruling if you decide to show it.
```
