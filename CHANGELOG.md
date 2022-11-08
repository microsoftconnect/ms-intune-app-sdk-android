Version 9.1.0
-------------
* The build plugin will now replace inheritance of `FileBackupHelper` with
  `MAMFileBackupHelper` and `SharedPreferencesBackupHelper` with `MAMSharedPreferencesBackupHelper`.
* Fix incompatibility with `androidx.lifecycle:lifecycle-runtime:2.5.0+` that caused crash due to
  missing hooks for ActivityLifecycleCallbacks.
* Fix ProGuard rule in SDK to keep only MAM classes that implement `@Keep` annotated 
  interfaces.

Version 9.0.1
-------------
* Fix offline behavior for `PackageManager` methods added in API 33.

Version 9.0.0
-------------
* Support for API 33 targeting.
* Add build plugin support for missing PackageManager method
  `getPackageInfo(VersionedPackage versionedPackage, int flags)`
* The Build Tool CLI now supports accepting a plain-text file for the input
  and output parameters.
* Include API 33 Photo Picker tool in the `PHOTO_LIBRARY` location 
  of `getIsOpenFromLocationAllowed` and `getIsSaveToLocationAllowed` SDK methods.
* The build plugin will now replace instantiation of `CloudMediaProvider` with
`MAMCloudMediaProvider`.
* Support predictive back gestures in MAM-owned activities.

Version 8.6.3
-------------
* Minor logging improvements to Gradle plugin.
* Add version check to Gradle plugin to prevent mamification when using
  Android Gradle Plugin 7.2.0/7.2.1 due to
  https://issuetracker.google.com/issues/232438924. Use AGP 7.1.3 and below or
  7.2.2 and above.

Version 8.6.2
-------------
* Update documentation for SaveLocation.LOCAL.

Version 8.6.1
-------------
* The MAM SDK will attempt to validate tokens returned from the app's
  MAMServiceAuthenticationCallback instance, and tokens acquired
  with the wrong parameters may be rejected.
* Add MAM Strict Mode check: 'UNMANAGED_CONTEXT_FOUND ' to validate that MAM found a managed `Context` to ensure policy enforcement.
  This would likely indicate a failure in the SDK surface modified by the build plugin or missing MAM SDK surface.

Version 8.5.0
-------------
* Expanded `getIsOpenFromLocationAllowed` and `getIsSaveToLocationAllowed` SDK method 
  to include `PHOTO_LIBRARY` location.
* Fix build plugin issue resuling in a `javassist.CannotCompileException` in
  certain projects.

Version 8.4.0
-------------
* The build plugin will now replace inheritance/instantiation of
  `SurfaceView`, `GLSurfaceView` and `VideoView` with MAM-specific replacements.
  These are used to enforce editor policy restrictions on SurfaceViews.
* Add ProGuard rule to SDK to fix R8 optimizations involving interfaces that have
  a single compile-time implementation. Currently, this is only known to impact
  scenarios involving MAMBackupAgentHelper.
* Fix synchronization issues arising when MAMServiceAuthenticationCallback instance is
  registered on a background thread.
* Add tracing and telemetry for monitoring and improving startup time.

Version 8.3.0
-------------
* Add new `com.microsoft.intune.mam.AllowIsolatedProcesses` manifest meta-data
  item to allow isolated process execution. MAM cannot apply protections to
  isolated processes. As the app developer, it is your responsibility to ensure
  that your isolated processes cannot expose organization data.
* Keep `MAMAppConfig` from being minified at build time.
* Remove `GET_ACCOUNTS` permissions from the SDK manifest. This permission was
  removed by Android in API23, which is the minimum supported version for MAM policy.

Version 8.2.0
-------------
* The build plugin will now wrap calls to various `JobService` methods.
  For multi-identity apps, the MAM SDK will not attempt to infer the
  identity for a `JobService` or its individual jobs. Users of `JobService`
  should take care to set an identity on the service context or background
  thread as required by their `JobService` implementations. Relatedly, users
  of `WorkManager` should take care to set a thread identity in
  `Worker.doWork()` as required by their `Worker` implementations.
  Avoid setting an identity on the `Worker` context, because this
  context is shared across `Worker` instances.
* Add `MAMUserStatusManager`, which may be used to check whether a
  user is clocked out.
* Add `CLOCK_STATUS_CHANGED` notification type. Apps may register for
  this to be notified when Intune detects that a user has clocked out
  or clocked-in again. No notifications will be delivered if policy
  does not require the user to be clocked in. Handling this
  notification is only necessary for apps which need to take extra
  action to present a better user experience. Intune will
  automatically apply any policies around clock-in regardless of
  whether the app handles this notification.
* Add `WIPE_COMPLETED` notification type. Apps may register for this
  to be notified when Intune has finished processing a wipe, at least
  as far as the current app process is concerned. Will be delivered
  after `WIPE_USER_DATA` or `WIPE_USER_AUXILIARY_DATA`. If the app
  reports a failure from its handler for the above notifications, this
  notification will *not* be sent. Listening for this notification is
  optional.
* Improved error messages for certain data decryption failures.
* Improvements to dialogs prompting the user to install or update the
  Company Portal in cases when the Play Store is not available.

Version 8.1.1
-------------
* Ensure MAM component initialization before execution of a
  `MAMContentProvider`.

Version 8.1.0
-------------
* Add the MAMLayoutInflaterManagement with build plugin support to handle application
  usage of custom LayoutInflator.Factory and LayoutInflator.Factory2 instances.

Version 8.0.0
-------------
* Enable Java 8 language feature support. SDK consumers must specify
  'JavaVersion.VERSION_1_8' in 'compileOptions' if using an Android
  Gradle Plugin version below 4.2.
* Rename the `MAMPolicyManager` method `getPolicy()` to
  `getCurrentThreadPolicy()` to avoid confusion. For a multi-identity
  app, this is usually not the method you want to use, unless you have
  set the thread (or process-wide) identity. The old name still exists
  for now but is marked as deprecated.
* `MAMAppConfig` will only read `com.microsoft.intune.mam.managedbrowser.proxyPacUrl.FailOpenEnabled`
  from the MAM app config channel and not from Android Enterprise.
* The build plugin now automatically includes all external libraries
  and the `includeExternalLibraries` configuration option has been
  removed.  This change was prompted by Android Gradle Plugin 4.2,
  which no longer exposes library names to the Transform API on which
  our plugin is built.
* The legacy Android Support Libraries are no longer supported. Apps
  are expected to be using AndroidX, either directly or through
  enabling the Jetifier.
* Fix bug in log pii filtering so null and empty strings are no longer hashed.
* Add MAM Strict Mode check: `CLEAR_PROTECTED_FLAG_SECURE` to
  ensure FLAG_SECURE isn't cleared when policy restricts screenshots.
* The build plugin will now wrap calls to various `AppSearchManager`
  classes/methods. This allows us to enforce transfer policy on data
  stored in the new centralized search index on Android 12.
* Add MAM Strict Mode check: 'INVALID_MAM_SERVICE_TOKEN' to validate user
  passed values for aadId & resourceId while acquiring MAMService token.
* Remove `MANAGE_ACCOUNTS` and `USE_CREDENTIALS` permissions from the
  SDK manifest. These permissions supported ADAL usage for auth policy
  and default enrollment, but were removed by Android in API23, which
  is the minimum supported version for MAM policy.
* Add MAM handling for the Android S data extraction rules for backup
  and restore. New `meta-data com.microsoft.intune.mam.DataExtractionRules`
  introduced that mimics the android:dataExtractionRules manifest tag.

Version 7.6.2
-------------
* Fix build plugin issue with methods which use a more-derived return
  type than the superclass method.

Version 7.6.1
-------------
* Fix authentication callback issue for Microsoft Defender.

Version 7.6.0
-------------
* Add MAM Strict Mode check: `CONTENT_INTENT_WITHOUT_IDENTITY` to
  check for intents started to transfer content to another app without
  an identity,while the foreground activity does have an identity
  set. This would likely indicates a failure to plumb through the identity.
* Add MAM Strict Mode check: `CONTENT_RESOLVER_NO_IDENTITY` to check
  that multi-identity apps using content providers set an identity on
  the context the resolver was retrieved from or on the
  thread/process.  Failure to do so indicates likelihood that the app
  is performing app-to-app communication on a background thread
  without proper consideration of what account the operation is
  running under.
* Add MAM Strict Mode check: `UPDATE_TOKEN_WITHIN_ACQUIRE_TOKEN` to
  check for calls to the MAMEnrollmentManager's updateToken() method
  from within the app-provided MAMServiceAuthenticationCallback's
  acquireToken() method. This is not the intended purpose of updateToken(),
  and could cause a deadlock.
* Add `MAMPolicyManager` method `getCurrentIdentity`. This is a
  convience method to consider the process, UI, and thread identities
  in priority order to allow the app to easily understand what MAM
  views as the effective identity.
* Remove ApplicationUpdateReceiver. This did not have relevance to
  most apps and its functionality is now accomplished without
  requiring a manifest-declared broadcast receiver.
* Provide meaningful names to Intune MAM threads.

Version 7.5.0
-------------
* Fix build-plugin issue where RC-suffixed Gradle versions would cause
  build failure.
* Add config mode for Microsoft Defender ATP.
* The build plugin now automatically includes all external libraries
  when used with Android Gradle Plugin 4.2 and higher. These versions
  no longer expose the library names to the Transform API which our
  plugin is built on. The `includeExternalLibraries` configuration
  option will be removed in MAM SDK 8.0.
* Add MAMCertificatePinningManager API for certificate pinning support.


Version 7.4.1
-------------
* Fix NullPointerException in Allowed Accounts.
* Fix an intermittent build plugin issue that impacts super calls
  that target non-parent, non-system classes. We previously failed
  to rewrite these super calls when the containing class is processed
  before its ancestor classes.
* Fix bounds-checking in MAMDataProtectionManager (small buffers could
  previously result in an BufferUnderflowException).
* The Build Tool CLI now supports incremental builds (via a new 
  --processed option) for parity with the Gradle plugin.

Version 7.4.0
-------------
* Report functionality is now supported in the command-line BuildTool,
  via the `--report` parameter. This functionality has been available
  in the Gradle plugin for some time.
* Add `AppPolicy` methods `diagnosticHasSaveRestriction` and
  `diagnosticHasOpenRestriction` which may be used by apps which (for
  example) wish to warn the user in advance when some operations may
  be prohibited by policy. They should not be used for enforcement --
  please continue to use `getIsSaveToLocationAllowed` and
  `getIsOpenFromLocationAllowed` for that purpose.
  
Version 7.3.1
-------------
* The build plugin will now replace inheritance/instantiation of
  `RelativeLayout` with `MAMRelativeLayout`. This is used to enforce
  keyboard restrictions in apps which create input connections from
  custom layouts.

Version 7.3.0
-------------
* Add MAM Strict Mode check: `AUTHENTICATION_CALLBACK_NOT_REGISTERED` to check that
  the MAMServiceAuthenticationCallback is registered in Application.onCreate().
* Reduce main-thread IO during app initialization.
* Add `AppPolicy` method `getIsOpenFromContentUriAllowed` to allow an
  app to test whether data ingress (receive) policy will block
  receiving data from the given URI. This is intended primarily as a
  convenience, it is not necessary for enforcement. MAM will continue
  to automatically block prohibited content provider queries/opens.
* Exclude all nested inner classes of classes excluded from mamificiation.
* Add MAMKeyNotAvailableException which is thrown from MAMDataProtectionManager when a 
  buffer cannot be decrypted due to the app no longer being managed.
* Fix build plugin to rewrite all super calls that target a replaced base class at any
  point in the inheritance chain. In v7.2.1, we introduced a similar fix to correctly
  rewrite super calls, but that fix only applied to methods that were renamed
  (e.g. onCreate() -> onMAMCreate()).
* Add `MAMFileProtectionManager.getProtectionInfo` overload which
  takes a content `Uri`. This should be used in preference to the
  overload taking a `ParcelFileDescriptor` when it's necessary to
  check a file's identity before reading it in order to perform the
  read under the correct identity.
* Replace MAM Strict Mode check `SAVE_TO_ODB_MISSING_UPN` with
  `SAVE_TO_LOCATION_MISSING_UPN` to properly reflect that the check
  covers scenarios beyond ODB and fix spurious error raised on empty
  UPN for ACCOUNT_DOCUMENT, which is allowed.
* Fix bug where we aren't correctly calling MAMBackupAgent.onMAMRestoreFinished()
  in offline mode.
  
Version 7.2.2
-------------
* The build plugin will now replace inheritance/instantiation of
  `ViewGroup` with `MAMViewGroup`. This is used to enforce editor policy restrictions on ViewGroup.

Version 7.2.1
-------------
* Fix build plugin bug that misses super call rewrites when the calls target an
  ancestor of the rewritten base class.

Version 7.2.0
-------------
* Add new `DATA_FROM_INTENT` option to `IdentitySwitchOption`.
* Improve build plugin classpath computation. This fixes missed
  project dependencies with Android Gradle Plugin 3.6.1+ as well as an
  illegal state exception during dependency calculation in Gradle 6.7.
* Fix bug causing repeated prompts to install the Company Portal on Android 11
* Correct enrollment retry logging.
* Add MAMIdentityRunnable as a convenience. Wraps another Runnable to execute under the given identity.

Version 7.1.0
-------------
* Updates `MAMAutoCompleteTextView` classes to enforce screenshot blocking policy when a screen recorder is in use.

Version 7.0.1
-------------
* Fix build plugin NPE when entites that aren't full-fledged classes end up on the classpath.
* Update certificate pins for connections to Intune services.

Version 7.0.0
-------------
* Android Gradle Plugin version 3.6.1 or higher is required.
* Update Javassist dependency to 3.27.0-GA.
* Remove `SecureBrowserPolicy` from the SDK. This class was exposed
  but was not documented. It was previously used by the Intune Managed
  Browser, which has since been superseded by Microsoft Edge.
* Support libraries no longer use AndroidX annotations due to tighter
  restrictions in the Android build system around Support and AndroidX 
  library coexistence.
* Support for targeting API 30, including new ContentProvider,
  ContentProviderClient, ContentResolver methods, and allow-list Company Portal against package visibility restrictions.
* Add `onMAMPictureInPictureRequested` to `MAMActivity` to ensure `onPictureInPictureRequested`
  is not called if launch if blocked during `onCreate`.
* `StrictScopedDisable` now extends from Closeable as well as
  AutoCloseable for compatibility with Kotlin's `use` extension
  method.
* The build plugin will now automatically include local AAR files
  (i.e. as opposed to AAR dependencies retrieved via artifact
  coordinates) for mamification. The Android Gradle Plugin transforms
  these files into jars with names which are inconsistent across AGP
  versions and often unrecognizable, making inclusion through
  `includeExternalLibraries` specification difficult.
* The build plugin will now replace inheritance/instantiation of
  `MediaRecorder` with MAM equivalent `MAMMediaRecorder`.
* The MAMBlobStoreManager replaces the BlobStoreManager with build plugin
  support, to enable protection of shared data blobs.
* Fix build plugin regression which could lead to bytecode
  corruption. Backported to 6.7.1.
* Expand `getIsOpenFromLocationAllowed` SDK method to handle a non-null username for identity-tagged files in local storage.
  For convenience, add an SDK method `getIsOpenFromLocalStorageAllowed` that accepts a `File` parameter.

Version 6.7.0
-------------
* Refine proguard rules to reduce the size impact of the SDK.
* Add `bypassConditionalLaunchChecks` to `MAMPolicyManager` interface
  to allow special-purpose Activities to avoid conditional launch checks.
* Suppress class format errors in plugin when `verify` option is enabled.
  Some classes from the Android 11 build tools are built with the Java 9 class
  format and cannot be verified when building with Java 8. These classes cannot
  be verified because they cannot be parsed in a Java 8 JVM. Updating to Java 9+
  will allow these classes to be verified.
* The build plugin will now replace inheritance/instantiation of
  `PopupWindow` with MAM equivalent `MAMPopupWindow`, `ListPopupWindow` with
  MAM equivalent `MAMListPopupWindow`, and `PopupMenu` with MAM equivalent
  `MAMPopupMenu`. This is used to enforce screenshot blocking policy when 
  a screen recorder is in use.
* If not using the build plugin, the replacements listed above must be made
  manually. Using the build plugin is very strongly recommended.


Version 6.6.1
-------------
* Fix build plugin compile-time error when Android Gradle Plugin 4.0
  is used with the Play Services Core library.

Version 6.6.0
-------------
* Add `onMAMUserLeaveHint` to `MAMActivity` to ensure `onUserLeaveHint` is not called
  if launch is blocked during `onCreate`
* When an app throws an exception from an implementation of
  `MAMNotificationReceiver` that exception is caught and the app is
  regarded as having failed to handle the notification as if it had
  returned `false` from `onReceive`. One impact of this is that if a
  wipe handler throws, the default wipe behavior will be applied
  rather than leaving the app in a crashing-and-not-wiped state.
* Fix theming issue in certain policy enforcement dialogs which where shown with a black background instead of a
  transparent background.
* Extend redirection to the Intune web portal for all app installation scenarios where Google Play Services are not
  available and links to market:// are not handled.
* Point to per-environment specific FWLinks for Company Portal installation when Google Play Services and links to 
  market:// are not available.
* Use android.R.attr.colorForeground instead of android.R.attr.textColorPrimary to apply textColor on all dialogs successfully
  when an app theme is given.

Version 6.5.6
-------------
* Fix theming issue in "Install Company Portal" dialog which has a black background instead of a transparent background.
  This regression was introduced in SDK 6.5.0"

Version 6.5.5
-------------
* `MAMAppConfig` will read the following configs from both MAM app config and Android Enterprise.
  * `com.microsoft.intune.mam.managedbrowser.bookmarks`
  * `com.microsoft.intune.mam.managedbrowser.homepage`
* `MAMAppConfig` will read the following configs from the MAM app config channel and not from Android Enterprise.
  * `com.microsoft.intune.mam.managedbrowser.account.syncDisabled`
  * `com.microsoft.intune.mam.managedbrowser.openInPrivateIfBlocked`
  * `com.microsoft.intune.mam.managedbrowser.durationOfOpenInPrivateSnackBar`
  * `com.microsoft.intune.mam.managedbrowser.NTLMSSOURLs`
  * `com.microsoft.intune.mam.managedbrowser.durationOfNTLMSSO`
  * `com.microsoft.intune.mam.managedbrowser.disableMvpn`
  * `com.microsoft.intune.mam.managedbrowser.proxyPacUrl`
* Mark Build Plugin Android Gradle Transform as cacheable for possible
  performance improvements
* Unconditionally register the Company Portal install receiver in Offline mode.
  This will reduce user logins during APP-CA sign-ins when Company Portal is not
  already installed on the device, at the expense of restarting MAM apps if the
  Company Portal is installed for no reason.

Version 6.5.4
-------------
* Add support for the Gallatin / Mooncake Sovereign Cloud.

Version 6.5.3
-------------
* Fix build plugin issue where not all libraries with dependencies on
  processed libraries were properly included for processing. This fix
  is necessary when upgrading to ADAL 3.0.1. Without this fix, if
  `verify=true`, in `intunemam` configuration in `build.gradle`, the
  issue would present as a build-time error. If verification is not in
  use, this issue would present as a runtime error for overriding a
  final method.

Version 6.5.2
-------------
* Build plugin support for Gradle 5.6/Android Gradle Plugin 3.6

Version 6.5.1
-------------
* Added support for a MAM Service feature to detect disabled AAD accounts.

Version 6.5.0
-------------
* Added support for custom themes. The app may provide a theme to the MAM SDK
  using `MAMThemeManager.setAppTheme` which will be applied to all 
  MAM screens and dialogs.
* Add MAM Strict Mode check: `APPLICATION_CONTEXT_DISCOVERED` which identifies when
  an Application context is discovered when an Activity context is expected.
  
Version 6.4.0
-------------
* Add a new method `MAMPolicyManager.showDiagnostics` that displays Intune
  MAM diagnostics information.
* Update login authority mapping to supported sovereign cloud endpoints.
* Use `Application.getProcessName` when possible (API 28+) instead of
  `ActivityManager.getRunningAppProcesses` as the latter is primarily
  intended for debugging.
* Add `getIsOpenFromLocationAllowed` SDK method for controlling data ingress.
* Add the `MAMUIHelper` class for displaying policy related UI.

Version 6.3.0
-------------
* Add MAM Strict Mode check: `NON_INTEGRATED_VIEW` which verifies that
  View classes are properly MAM-integrated.
* Update button label "Go Back" to "Close" to better reflect actual behavior.
* After displaying an offline wipe notification, resume launch of the
  app's activity rather than killing it.
* Changed the header name for the retry interval that controls the enrollment
  retries for unlicensed clients, in accordance with a service-side change.

Version 6.2.2
-------------
* Fix plugin build error if the `excludeClasses` specification was empty
  (regressed in 6.2.0)

Version 6.2.1
-------------
* Fix missing Javadoc for MAM Strict Mode

Version 6.2.0
-------------
* Added MAM Strict Mode which uses heuristics to detect mistakes in
  usage of MAM APIs or MAM-restricted platform APIs. Your team is
  strongly encouraged to use it in internal debug/develop/dogfood
  builds. The build plugin writes some additional metadata to facilitate MAM Strict Mode.
* Added `MAMAccountAuthenticatorActivity` (MAM version of `AccountAuthenticatorActivity`).
* Added support for view `onCreateInputConnection`.
* Fix a bug with service URL parsing.

Version 6.1.0
-------------
* Allow the Intune service to configure retry intervals for users not yet licensed or policy-targeted.
* The `android:testOnly` attribute no longer causes apps to
  automatically connect to the Test Agent instead of the Company
  Portal. This behavior previously caused confusion for several
  teams. If you use the Test Agent, it is necessary to set both
  `testOnly` and a new meta-data item:
  `<meta-data android:name="com.microsoft.intune.mam.Agent" android:value="test" />`
* Add handling for MSAL-style ids for identity comparisons.  Note
  that this is not full support for the use of MSAL.

Version 6.0.3
-------------
* Exclude META-INF classes from processing in the build plugin. This
  fixes a build-time error encountered by one SDK consumer.

Version 6.0.2
-------------
* `MAMAppConfig` will only read `com.microsoft.intune.mam.managedbrowser.AllowTransitionOnBlock `
  and `com.microsoft.intune.useEdge` from the MAM app config channel and not from Android Enterprise.
* Improve Company Portal update dialog for devices without the Play Store.

Version 6.0.1
-------------
* For Xamarin apps, correct an issue in the SDK bindings that prevented
  IntentServices from starting correctly.

Version 6.0.0
-------------
* Add nullability annotations in the SDK. This introduces a dependency
  on `androidx.annotation:annotation:1.0.0`.
* Remove DownlevelStubs JAR which was replaced by an AAR in 5.8.0.
* Support for targeting API 29, including new `ContentProvider`,
    `ContentProviderClient`, and `ContentResolver` methods.
* Add override for `notifyAsPackage` method introduced in API 29.
* Remove no-longer-necessary Proguard rules.
* Fixed issue where enrollment would fail when apps used domain-specific
  configurations in `network_security_config.xml`

Version 5.9.0
-------------
* Make build plugin classpath computation more deterministic to avoid
  intermittent edge-case compilation errors. Build plugin output
  should not be affected.
* Fix missing info in certain telemetry events.
* Fix a potential issue where install Company Portal dialog may not
  show on Q devices when user navigates away from the app before
  authentication completes.
* Add incremental build support to build plugin.  Incremental build
  support is experimental, and is off by default. To enable it, specify
    `incremental=true` in the `intunemam` configuration block in `build.gradle`.
* Add notification restriction policy. Apps must check the result of the
  `getNotificationRestriction` method in `AppPolicy` before showing a notification
  associated with a given user. If this method is not invoked, notifications
  will be blocked automatically in single-identity apps.
* Only allow `IntuneMAMOnly` AppConfig keys via the MAM delivery channel.

Version 5.8.1
-------------
* Fix bug in implicit wipe (primarily when Company Portal is
  uninstalled unexpectedly) where `onStart`/`onMAMResume` may be called
  without `onMAMCreate` being called.

Version 5.8.0
-------------
* Fix build plugin error related to processing transitive
  dependencies. If your app builds without issue, it is not affected
  by this bug.
* Fix build plugin processing of AIDL-generated files.
* Ensure MAM component initialization before execution of a
  `MAMBroadcastReceiver`. This is a speculative fix for a rare crash.
* Remove unneeded IPC calls related to identity persistence.
* New build plugin configuration option: `verify`. This acts as a guard
    to ensure many types of potential plugin bugs will produce
    compilation failures instead of runtime failures. To use it, specify
    `verify=true` in the `intunemam` configuration block in `build.gradle`. Verify
    defaults to false, though this default may change in the
    future.
* Fix build plugin error where Jetified libraries with an undeclared
  support library dependency were not correctly processed.
* The build plugin will now replace `NotificationManager.notify` calls with
  calls to `MAMNotificationManagement`, and `NotificationManagerCompat.notify`
  calls with calls to `MAMNotificationCompatManagement`.
* Fix crash in `MAMPrintHelperManagement`. If your app uses `PrintHelper`
  from the legacy support libraries, it should take this update.

Version 5.7.1
-------------
* Fix SDK 5.4.0 regression in `MAMAlertDialogBuilder` causing
  application crash due to build plugin rewrite of
  `AlertDialog.Builder`. `MAMAlertDialogBuilder` is no longer marked as
  `final`. Backported to 5.6.2.

Version 5.7.0
-------------
* Convert `DownlevelStubs` from JAR to AAR so consumers do not need to specify
  their own ProGuard rules.
* Remove deprecated telemetry events.
* Fix build plugin error that could fail compilation through
  too-aggressive rewriting. If your app builds without issue, it is
  not affected by this bug.
* Fix build plugin compatibility with the AndroidX Jetifier. Backported to 5.6.2.
* Do not force app restart on `ACTION_PACKAGE_CHANGED` for Company Portal.
* Throttle severe messages logged to telemetry by unique message and stacktrace.
* Add missing override of `Activity.startActivityIfNeeded`.

Version 5.6.1
-------------
* Handle `CertificateException` thrown during Company Portal signature
  verification in automated testing.

Version 5.6.0
-------------
* The build plugin will now replace inheritance/instantiation of
  `WebView` with `MAMWebView`. This is used on Android O+
  for transfer policy enforcement on text classifier actions
* The build plugin now bypasses jar verification. Jars with invalid
  signatures will not cause it to fail.	
* Fix `MAMResolverActivity` breaks multi-process activity stack.
* Reduce telemetry noise by downgrading a severe message from an expected AAD change.
* The interval for enrollment retries for accounts that are not licensed or targeted with
  policy is reduced to 12 hours.

Version 5.5.0
-------------
* The build plugin will automatically include external libraries which
  depend on an included external library.
* The build plugin will rerun if the `build.gradle` file changes
  (because the `intunemam` block may have changed).
* Increase enrollment retry backoff on network failures.
* The build plugin will now wrap calls to `View.startDragAndDrop` and `DragEvent.getClipData`.
  This allows us to enforce transfer policy on drag and drop without relying on Android internals.
* Fix isolated process crashes when call into `MAM*Management` methods.
* Introduce options for UI identity switches. New overrides have been added for `switchMAMIdentity`
  and `setUIPolicyIdentity` that take a set of `IdentitySwitchOption` values.
* Improve offline performance when Company Portal is not on device.
* `MAMComplianceBlockActivity` is no longer exported.
* The default behavior of `MAMActivity.onSwitchMAMIdentityComplete` has
  changed. If the identity switch failed, the default behavior is now
  to finish the activity. The previous default of taking no action
    made data leaks easy if the app did not pay close attention to the
    asynchronous completion result of the switch. There is no change in
    behavior for activities which do override
    `onSwitchMAMIdentityComplete`. If your app expects identity switches
    to be cancellable within the same activity, you must override
    `onSwitchMAMIdentityComplete` and take appropriate action.

Version 5.4.0
-------------
* Build plugin no longer tries to rewrite non-existent `Fragment.onCreateDialog` method.
* Blocking UI is not shown until all offline wipes are complete to avoid race conditions.
* Added an `areIntentActivitiesAllowed` method to `AppPolicy`, allowing an
  app to determine whether all apps able to handle a given intent
  would be blocked by policy.
* Build plugin rewrite rule for the `AlertDialog.Builder` has been corrected.

Version 5.3.2
-------------
* Ensure `MAMComponents` initialized in `MAMActivity`. This is a
  speculative fix for crashes occurring in Launcher.
* Remove reference to the ADAL `StringUtil` class. This was causing
  consistent crashes in Launcher, because ADAL is not included
  as a dependency in the SDK.

Version 5.3.1
-------------
* Append MAM service URL queries with device and MAM app information
* The build plugin can write a report of the changes it makes. Specify
  `"report = true"` in the intunemam configuration block. Logs will be
  written to outputs/logs in the build directory.
* Wipe on Company Portal uninstall is now robust to the app being
  unable to start completely without access to encrypted files
* After an implicit wipe completes, the MAM enrollment status cache
  is cleared and the appropriate wipe notice flag is set in a single
  transaction.
* Fix issues when a `Service` is created (by Android) before the `Application`.
* Return `NOT_LICENSED` for Blackforest and Gallatin enrollments.
* Add additional telemetry data for SSL pinning failures.

Version 5.3.0
-------------
* Only support TLS 1.2 protocol above Jelly Bean
* Update MAM AppConfig to support Android Enterprise AppRestrictions.
* The build plugin will now wrap calls to `ContentResolver` and
  `ContentProviderClient`. This allows us to enforce parts of
  transfer/receive policy without relying on Android internals.
* Fix an NPE in the build plugin if `JavaCompile.getOptions()` returns null
* Fix a bug where a wipe could occur if the app was started while the
  Company Portal required a version update.
* The build plugin now works around a Gradle 4.8+ bug in decoration of
  signed plugins causing configure failure. See gradle issue 
  [6860](https://github.com/gradle/gradle/issues/6860)

Version 5.2.0
-------------
* Add the `MAMComplianceManager` interface with new API to support the
  MAM-CA compliance flow.
* Added `MAMAlertDialogBuilder` to create managed `AlertDialog` 
  (with a support class version).
* Fixed bug where multi-process apps didn't use the registered
  data wipe handlers in secondary processes.
* Added tracked occurrence telemetry for SSL certificate pinning failures.
* Fixed bug in accessing uninitialized components during service start.
* The build plugin will now wrap calls to `PrintManager` and
  `PrintHelper`. This allows us to enforce print policy without relying
  on Android internals.

Version 5.1.0
-------------
* The build plugin now supports negation patterns in the
  `includeExternalLibraries` configuration to exclude libs which would
  otherwise be included by a wildcard pattern.
* Fix build plugin bugs:
  1. No longer use the JRE system classpath at all. Doing so was
     incorrect and unnecessary. There is no expected impact to any
     apps from this.
  2. Fix incorrect rewriting of new-array expressions
  3. Fix a bug blocking use of Instant Run. We believe Instant Run
     will work as expected now, but please let us know your
     experience.
  4. Correctly process app classes placed under the android. package
  5. Correctly find inner classes of Kotlin classes.
* Added `AllowedAccounts.unregisterListener` method. Note that it is
  generally recommended to leave a listener live for the process
  lifetime.

Version 5.0.2
-------------
* Fixed NPE in build plugin when some subprojects do not use the Android plugin.	

Version 5.0.1
-------------
* Fix a bug with build plugin support for `BackupAgent` and `BackupAgentHelper`.

Version 5.0.0
-------------
* The build plugin will now wrap all `ClipboardManager` calls to query or
  set the primary clip in calls to `MAMClipboard`.
* The build plugin will now wrap most `PackageManager` calls in calls to
  `MAMPackageManagement`. `PackageManager` calls will not be intercepted automatically
  on Android P.
* The build plugin will now wrap the `DownloadManager.enqueue` call in a call to
  `MAMDownloadManagement`. `DownloadManager` calls will not be intercepted automatically
  on Android P.
* The build plugin will now replace inheritance/instantiation of
  `TextView` (and derived views, such as `EditText`) with MAM equivalents
  (`MAMTextView`, `MAMEditText`, etc). This is used on Android P for
  clipboard policy enforcement and for transfer policy enforcement on
  text classifier actions
* If not using the build plugin, the replacements listed above must be made
  manually. Using the build plugin is very strongly recommended.
* Add `MAMBackupDataInput` to the SDK and signatures of `BackupAgent.onMAMRestore` and 
  `BackupAgentHelper.onMAMRestore` for identity backup.
* Add support for new (Android P) `BackupAgent.onRestore` overload to `MAMBackupAgent`.
* Fix missing handling of `Activity.startActivities`.
* Build plugin bug fixes.

Version 4.7.0
-------------
* The build plugin now supports exluding specific variants from processing.
* The build plugin now rewrites all MAM overloads for `DocumentsProviders`.
* Fix build plugin failure if app activity derives a library project activity.
* Build plugin `includeExternalLibraries` specification no longer
  requires a version component for artifact notation
* Do not log to logcat in production builds.
* Reduce dependence on runtime-emitted stub
  classes. `MAMDocumentsProvider.findDocumentPathMAM` returns an `Object`
  to remove the need for DocumentsContract$Path to exist during
  reflection of `MAMDocumentsProvider` (it doesn't prior to API26).

Version 4.6.0
-------------
* Improve performance in offline scenario when the Company Portal app is not on device
  by providing an option to disable MAM offline logging.
* Improvements to the Build Plugin. It is now supported for production use.
* Add static version of `MAMContentProvider.isProvideContentAllowed` for use with the build plugin.
* Separate `MAMActivityIdentityRequirementListener`/`MAMIdentityRequirementListener`
  interfaces out of `MAMActivity`/`MAMService`/`MAMContentProvider` for use
  with build plugin.
* Fix isolated process crashes on API 8.0 and up.
* Improve enrollment telemetry by reporting more fine-grained failure causes.
* Restrict MAM-WE enrollment retries to primary process to avoid race conditions.
* Add wipe reason to selective wipe telemetry.

Version 4.5.0
-------------
* Fix portal reinstallation wait loop to be correctly bounded.
* Improve performance in offline scenario when the Company Portal app is not on device.
* Add Sovereign Cloud support via a new `registerAccountForMAM` that accepts the user's
  authority - Arlington is supported. New sovereign clouds will be supported
  via SDK updates but no additional source integrations will be necessary.
* Fix SDK 4.4.2 regression in `MAMDialogFragment` causing application crash.

Version 4.4.2
-------------
* Fix `NullPointerException` if `onAttach` is not the first `MAMFragment` method called.
* Fix `ArrayIndexOutOfBoundsException` for `testOnly` builds if the process is started
  by a component with `android:isolatedProcess="true"` flag.

Version 4.4.1
-------------
* Minor fix to `MAMApplication.attachBaseContext` handling. Always call
  `super.attachBaseContext` even if invoked more than once.

Version 4.4.0
-------------
* SDK now supports targeting API 27
* Fix crash in conditional launch dialog on API 26 devices for apps that target API 27.
* Prevent proguard from marking classes/methods as `final`/`private` as
  this interferes with proxy generation
* Various improvements to Aria telemetry.
* Retry initial enrollment failures more frequently if they did not result in service load

Version 4.3.0
-------------
* Fix `MAMAsyncTask` so it does not hold onto Context references for longer than needed.
* Send tracked occurrence and service request telemetry to Aria.
* Stop sending error event telemetry to Asimov.
* Allow connecting to Company Portal instead of TestAgent even for
  apps with the `testOnly` attribute. This is enabled by adding the
  boolean `meta-data com.microsoft.intune.mam.ForceProductionAgent`.

Version 4.2.1
-------------
* Fix PII logging leak of user UPN.
* Properly block activity launch for multiple identities in
  `COMPANY_PORTAL_REQUIRED` state
* Updated localizations for Allowed Accounts
* Send SLA telemetry to Aria, with new mechanism for tracking duration.

Version 4.2.0
-------------
* Add `AllowedAccounts`, allowing an app to query whether the set of
  accounts it is allowed to sign in is limited.
* Use https for all network calls to support apps which set
  `android:usesCleartextTraffic="false"`

Version 4.1.0
-------------
* When the Company Portal is not installed, `MAMUserInfo.getPrimaryUser`
  will now return a non-null result only when enrollment has been
  attempted for a user which is actually targeted with policy, not
  merely Intune licensed.
* Add `MAMAsyncTask` as a convenience wrapper around `AsyncTask`. When
  used, it ensures that the background thread runs under the same
  identity as the activity.
* Add `MAMMediaMetadataRetriever` as a drop-in replacement for
  `MediaMetadataRetriever` which allows working with encrypted media
  files. Apps should replace usage of `MediaMetadataRetriever` with
  `MAMMediaMetadataRetriever`.
* Add `Microsoft.Intune.MAM.SDK.DownlevelStubs.jar` as an optional
  separate library which apps can incorporate if they need to perform
  reflection on classes deriving from `MAMActivity`. If your app did not
  previously experience issues around reflection and Intune
  integration, there is no reason to consume this library.
* Fix issue where `onMAMPrepareOptionsMenu` could be called before `onMAMCreate`.

Version 4.0.0
-------------
* Add new, API26 functions to `MAMContentProvider`, `MAMDocumentsProvider`, `MAMFileProvider`,
  `MAMPendingIntent` and `MAMMediaPlayer`.
* Add new, API26 class `MAMJobIntentService`.
* Remove `MAMActionBarActivity` from `Microsoft.Intune.MAM.SDK.Support.v7.jar` because it was
  removed from the Android support libs with version 26.0.0.
* Add `MAMBackgroundJobService` to the SDK to comply with API26 background execution requirements.
  This is a MAM internal only change and no partner interaction is required.
* Send severe telemetry to new telemetry pipeline (Aria). Add selective wipe event.
* Version 1 of the MAM-WE enrollment API is now completely removed.
* Provide a default implementation for `getAdalSecretKey()`. Apps using Version 2 of the 
  MAM-WE enrollment API no longer need to override this method.
* Add MAM dialog UI update that was removed in SDK 3.1.2.

Version 3.1.2
-------------
* Remove MAM dialog UI update introduced in SDK 3.1.1. This change
  resulted in broken UI under some circumstances. It will be
  reinstated in a forthcoming SDK update. Any app which updated to SDK
  3.1.1 should immediately update to 3.1.2

Version 3.1.1
-------------
* Fix logging of some exceptions
* Update MAM dialogs to match the Material design guidelines on API 21 and above.
* Display a non-blocking "You need to update your Android device" deprecation warning, which is
    a one-time dialog, shown at managed app launch, similar to "Managed by your organization" message.

Version 3.1.0
-------------
* Allow Company Portal installation detection to close the app on Android O
* Certificate pinning for the MAM services.
* `MAMFileProtectionManager` now provides an overload of the protect
  method which allows set an identity on a `ParcelFileDescriptor`. This
  is intended for use when storage volumes are accessed through the
  Storage Access Framework. It cannot be used to set an identity on
  files provided by other applications.
* Add `AppPolicy` method `diagnosticIsFileEncryptionInUse` which allows an
  app to find out whether MAM file encryption is being used. This is
  intended for diagnostic purposes only.

Version 3.0.1
-------------
* Set `android:multiprocess=true` for `MAMStartupActivity` and other
  similar activities. This addresses issues that occur when a
  multi-process app tries to start a MAM-integrated activity from a
  process other than the main process.
* Improve MAM telemetry via logging modifications
* Reduce telemetry noise by downgrading a severe message from an expected error.
* Fix `onMAMPrepareOptionsMenu` not being called when Company Portal not installed.

Version 3.0.0
-------------
* Version 1 of the MAM-WE enrollment API is now deprecated.  It will be removed completely
    at the next major version increment.
* Mark `onPrepareOptionsMenu` as `final`. Activities which previously implemented 
    `onPrepareOptionsMenu` must override `onMAMPrepareOptionsMenu` instead
* Mark `MAMFileProvider.call` as `final`. Apps which previously implemented call
    must override `callMAM` instead.
* Prevent erroneously logging a severe message.
* Improve locking in log handling. 

Version 2.3.0
-------------
* Add `MANAGEMENT_REMOVED` notification. This can be registered for in
    the same manner as `WIPE_USER_DATA` or `REFRESH_POLICY`. It is sent
    immediately before the app becomes unmanaged. Data protected with
    `MAMDataProtectionManager` should be unprotected as it will become
    inacessible once this notification returns.
* The MAM SDK is verified to work correctly with Android Support Library version 25.1.0.
* Handle `MessageFormat` logging more robustly.
* Add DNS lookup times to network operation telemetry data.

Version 2.2.1
-------------
* Add notification dialog if Selective Wipe was triggered implicitly.
* Update telemetry to include the mechanism used to acquire MAM service token.
* Add a guard to `attachBaseContext()` in `MAMApplication` to ensure that initialization is only done once.
    This is needed to support a new partner team's use case and doesn't affect typical usages.

Version 2.2.0
-------------
* Added new MAM-WE account registration API, which should replace the existing enrollment API.
    The existing enrollment API will be deprecated at the next major version increment.
* Distribute proguard.txt in the MAMSDK AAR file and alongside the jar. Note that the rule
    `-keepattributes Exceptions` is a newly discovered requirement

Version 2.1.6
-------------
* Fix race condition in offline MAM-WE cache that was causing JSON deserialization failures.

Version 2.1.5
-------------
* Clear the setting for showing the "Your organization protects data in this app" dialog after a selective wipe.
* Fixed the hardware back button does not dismiss the "Go to Store" dialog.
    
Version 2.1.4
-------------
* Fixed bug where app is incorrectly restarted following MAM enrollment.
    Now all Activities will be finished, not just `MAMActivity`.

Version 2.1.3
-------------
* Handle `COMPANY_PORTAL_REQUIRED` in `getIsIdentityManaged`.
    
Version 2.1.2
-------------
* Update telemetry events to more precisely track enrollment duration.
* Clear enrollment cache tracking upon offline wipe. This clears any
    potential PII. Also update cache for consistency and register a
    receiver in the `COMPANY_PORTAL_REQUIRED` throttled case, to avoid
    users getting in possibly irreconcilable state.

Version 2.1.1
-------------
* Added the device network info to the data collected by telemetry.

Version 2.1.0
-------------
* New method `AppPolicy.getAreScreenshotsAllowed`. This method will return
    false if the policy restricts taking screenshots
* Add missing override for the call method to `MAMFileProvider`. Apps should
    override `callMAM` instead. The call method will be marked as `final` in the
    next major release of the MAM SDK
* Updated translations for some strings
* Added the AAD ID of the user's tenant to the data collected by telemetry during app enrollment.

Version 2.0.0
--------------
* Beginning of new release cadence
* Added delayed loading of MAM internal.
    MAM internal libraries are not loaded unless MAM policy is deployed to an app on the device.
* Added crash handling to MAM initialization 3 consecutive crashes in
    MAM initialization will result in MAM no longer loading, and
    instead blocking the app if policy is deployed.
* MAM version of `onPrepareOptionsMenu`. Applications are encouraged to
    override `onMAMPrepareOptionsMenu` instead of
    `onPrepareContextMenu`. This will be enforced in the next major
    release.
* Changed Policy Required string from "This app requires your device to be enrolled using Microsoft
    Intune and to be compliant with your company's policies. Contact your IT department for help."
    to "This app requires management by Microsoft Intune. Contact your IT department for help."
* Added `MAMAppConfigManager` to Interface to facilitate passing of Application Configuration data from the MAM Service
    This includes an offline implementation of `MAMAppConfigManager` for use when Application Configuration data cannot be accessed.
