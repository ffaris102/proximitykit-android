## Version 0.12.3 - November 7, 2016

Bug Fixes:

- Fix bug causing rapid kit syncs when setting a custom sync interval


## Version 0.12.2 - October 27, 2016

No public facing changes


## Version 0.12.1 - September 16, 2016

Bug Fixes:

- Fix crash on wifi or power state change which occurs before a Proximity Kit
  manager has been created
- Fix race condition queuing multiple syncs on wifi or power state change
- Respect configured sync interval when syncing on wifi or power state changes
- Use sync interval provided in kit config
- Prevent duplicate geofence enter events on sync


## Version 0.12.0 - September 7, 2016

Enhancements:

- Regenerate app id if value becomes corrupted
- Add support for version 9.x of the Google Play services library
- Adds config setting `KitConfig.ALLOW_UNSUPPORTED_GOOGLE_PLAY` to by-pass the
  Google Play version check. This may result in a runtime crash as it will
  allow running against untested versions of Google Play.
- Begins including nullability annotations to provide better insight into the
  public API and help developers guard against `NullPointerException` only when
  necessary. See https://developer.android.com/studio/write/annotations.html
  for further details.
- Support caching multiple kits
- Retry analytic data upload when no network is available
- Attempt analytic upload with out delay; previously there was a 30 second
  delay from the closing of a session and the upload
- Attempt analytic upload on successful kit sync
- Add ability to transmit beacon advertisements on supported hardware

Bug Fixes:

- Improve thread safety around starting and stopping Proximity Kit Manager
- Improve thread safety around background time sync; ensuring only a single
  sync task is ever queued at a time
- Fix geofence region monitoring race condition which may exclude a region
- Monitor existing geofences on `start` from local cache
- Fix loading kit cache on unavailable network
- Fix race condition which may drop some analytic data
- Fix queuing duplicate analytic upload job on upload error
- Move analytic upload off `AsyncTask` queue on Android Honeycomb and newer
- Prune analytic data after upload

Breaking Changes:

- Not exactly a _breaking_ change but a slight behavior modification. The
  `MissingKeyException` and `MissingValueException` classes now do _not_
  automatically append the key name to the provided detail message. This change
  is to fall more inline with how existing exceptions work as well as provide
  better support for internationalized messages in the future.

Deprecations:

- The `LicenseManager` is a legacy internal implementation detail. It is in the
  process of being phased out. In this initial round the following publicly
  exposed methods are now deprecated and have been turned into no-ops:

  - `LicenseManager.getServerCheckCount()`
  - `LicenseManager.resetLicenseCheck()`
  - `LicenseManager.validateLicense()`
  - `LicenseManager(Context, Configuration, String, Date)`
- `KitOverlay()` is an internal implementation detail which was not meant to be
  exposed in the public API. It may be retained only as a private method in a
  future release.
- Deprecate support for 4.x - 7.x of the Google Play service library.

  This only adds a warning message to the logs when an app is built against a
  version of Google Play services in this range. Apps will continue to function
  as expected but developers are encourage to move to a newer version of Google
  Play.

  For historical reference version 4.4.x of Google Play services was released
  in May 2014. The most recent deprecated version, 7.8.x, was released in
  August 2015.


Version 0.11.0 - July 21, 2016
------------------------------

Enhancements:

- Report OS platform (as Android) with analytics and syncs
- Add name attribute to `ProximityKitGeofenceRegion#getName`
- Include local device bluetooth state on server sync
- Guard against remote exception when processing ranging events

Breaking Changes:

- Drop support for scavenger hunt configuration with removal of
  `ProximityKitManager#restart(String)`


Version 0.10.2 - March 14, 2016
-------------------------------

Bug Fixes:

- Fix `NullPointerException` which occurs when enabling geofences for a kit
  that is not setup with a map
- Fix a `NullPointerException` caused by disabling geofences while a sync is
  being processed
- Fix issue where removing the map from a kit does not clear monitored
  geofences after a sync
- Perform beacon and geofence monitoring/ranging changes prior to calling
  `ProximityKitSyncNotifier#didSync`


Version 0.10.1 - February 23, 2016
----------------------------------

Bug Fixes:

- Update to [AltBeacon/android-beacon-library](https://github.com/AltBeacon/android-beacon-library)
  version [2.7.1](https://github.com/AltBeacon/android-beacon-library/releases/tag/2.7.1)
  to fix a race condition which could cause scanning to stop


Version 0.10.0 - January 26, 2016
---------------------------------

Enhancements:

- Start monitoring geofences immediately when enabling them after the
  `ProximityKitManager` has been started; this provides improved support for
  Android 6 app which need to request runtime permissions
- Support custom `KitConfig` on initialization of `ProximityKitManager` via
  the factory `ProximityKitManager#getInstance(Context, KitConfig)`
- Support disabling cellular data for network communications through a flag in
  the `KitConfig`
- Add support for kit specific analytics URLs (see `Kit#getAnalyticsURL`)
- Use the analytics URL synced with a kit if one is not already set

Bug Fixes:

- Fix timestamp formatting in analytics data to properly send UTC times
- Improve handling of aggregate regions in analytics sessions
- Do not overwrite the timestamps when closing of analytic sessions
- Improve how analytics are recorded during ranging events
- Improve thread safety by synchronizing `ProximityKitManager` starting,
  stopping, feature toggles, and setting of callback notifiers.
- Fix a `NullPointerException` caused by stopping the manager while a failed
  sync was being processed
- Close input streams after reading network responses
- Fix race condition when accessing, the now deprecated (see below), factory
  `ProximityKitManager#getInstanceForApplication(Context)` from multiple threads
  prior to the initial creation of a `ProximityKitManager`
- Improved network and battery savings by defaulting to syncing Status Kit
  updates every 2 minutes instead of 30 seconds

Deprecations:

- Deprecate all of the following in favor of using the new configuration
  friendly factory `ProximityKitManager#getInstance(Context, KitConfig)`:

  - `ProximityKitManager()`
  - `ProximityKitManager(Context)`
  - `ProximityKitManager#getInstanceForApplication(Context)`
  - `ProximityKitManager#setConfiguration(String, String)`
  - `ProximityKitManager#setConfiguration(String, String, String)`
  - `ProximityKitManager#setPartnerIdentifier(String)`
  - `ProximityKitManager#clearPartnerIdentifier()`
- Deprecate `ProximityKitManager#restart()`, no replacement
- Deprecate all of the following in favor of using the new configuration
  friendly constructor `Configuration(Context, KitConfig)`:

  - `Configuration(Context)`
  - `Configuration(Context, Configuration, String, String)`
  - `Configuration(Context, Configuration, String, String, String)`
- Deprecate all of the following in favor of using the new configuration
  friendly setter `LicenseManager#reconfigure(Configuration)`:

  - `LicenseManager#reconfigure(Context)`
  - `LicenseManager#reconfigure(String, String)`
  - `LicenseManager#reconfigure(String, String, String)`


Version 0.9.0 - October 5, 2015
-------------------------------

Enhancements:

- Add an extra server sync on power connection
  Allows an app put into AppStandby mode on Android 6.0+ to still sync even though
  its access to network has been restricted when power is not connected.
- Update to [AltBeacon/android-beacon-library](https://github.com/AltBeacon/android-beacon-library)
  version [2.6.1](https://github.com/AltBeacon/android-beacon-library/releases/tag/2.6.1)


Version 0.8.0 - September 9, 2015
---------------------------------

Enhancements:

- Add support for version 7.x of the Google Play Services library.

Bug Fixes:

- Fix `NullPointerException` caused by an intent for a non-geofence transition
  alert


Version 0.7.0 - July 30, 2015
-----------------------------

Enhancements:

- Add `ProximityKitManager#setPartnerIdentifier(String)` and
  `ProximityKitManager#clearPartnerIdentifier()` to the public API. Allowing
  the configuration of an additional custom identifier which is attached to
  analytic events.


Version 0.6.3 - July 30, 2015
-----------------------------

Bug Fixes:

- Move calls to Status Kit analytics into a custom serial background queue;
  thus no longer consuming resources from `AsyncTask`'s thread pools
- Update to [AltBeacon/android-beacon-library](https://github.com/AltBeacon/android-beacon-library)
  version [2.3.5](https://github.com/AltBeacon/android-beacon-library/releases/tag/2.3.5)
- Fix edge case on spotty, slow, or no networks which causes every ranged
  beacon event to attempt a status update over the network.


Version 0.6.2 - May 28, 2015
----------------------------

Bug Fixes:

- Fix issue with ranging where the ranging timestamp is incorrectly store as a
  future time due to background task execution delays
- Fix issue where a group of beacons are ranged at the same time but may report
  slightly different timestamps


Version 0.6.1 - May 5, 2015
---------------------------

Bug Fixes:

- Fix timestamp parsing and formatting bug which can occur when Director is
  called from multiple threads.


Version 0.6.0 - April 28, 2015
------------------------------

Enhancements:

- Support for [Director](https://director.radiusnetworks.com) analytics.


Version 0.5.0 - April 10, 2015
------------------------------

Enhancements:

- Add support for explicit region monitoring and ranging.

  Previous functionality can be achieved by not checking "Explicit Region
  Monitoring" on the kit's "Properties -> Settings" page on the server. In
  order to control which regions are ranged and monitored ensure this option is
  checked.

  On the server, regions have an "Advanced Settings" option where you can check
  "Enable Ranging" and/or "Enable Region Monitoring".

Deprecations:

- The `ProximityKitManager#getMaxRegionsBeforeRollup` and
  `ProximityKitManager#setMaxRegionsBeforeRollup` methods are now no-ops. Use
  the new explicit region monitoring behavior instead.
- The `KitBeacon#to_region` conversion method was never intended to be
  `public`. To convert a `KitBeacon` to a `Region` construct a new region using
  the identifiers from the beacon.


Version 0.4.0 - March 16, 2015
------------------------------

Enhancements:

- Add support for version 6.1 and 6.5 of the Google Play Services Library.

Bug Fixes:

- Update to [AltBeacon/android-beacon-library](https://github.com/AltBeacon/android-beacon-library)
  version [2.1.4](https://github.com/AltBeacon/android-beacon-library/releases/tag/2.1.4)


Version 0.3.1 - March 11, 2015
------------------------------

Bug Fixes:

- Remove static compiled 'support-v4' library.


Version 0.3.0 - February 20, 2015
---------------------------------

Enhancements:

- Add ability to set a threshold on how many regions can be registered before
  being automatically combined. This allows for large region sets but keeps the
  device from getting overwhelmed.

Bug Fixes:

- Fix `NullPointerException` which occurs when requesting a Kit's overlays when
  it does not have a map.  (RadiusNetworks/proximitykit-android/#2)
- Fix bug preventing geofencing from working with API levels below 18.
  Geofences rely on Google Play services so they still require a min Android
  API level of 9.
- Reduce the `minSdkVersion` to 4; this is the lowest function API level which
  Proximity Kit will work with.
- Update to [AltBeacon/android-beacon-library](https://github.com/AltBeacon/android-beacon-library)
  version [2.1.3](https://github.com/AltBeacon/android-beacon-library/releases/tag/2.1.3)
- Prevent services from being exported system wide.


Version 0.2.0 - October 27, 2014
--------------------------------

Enhancements:

- Add `ProximityKitManager#stop()` to the public API

  This stops any further syncing with the cloud. It additionally, unregisters
  any beacons and geofences which are being monitored.
- Attempts to auto update database of Android model specific distance
  calculations
- Added generation of beacon bytearrays from BeaconParser in support of future
  transmitter on release of Android


Version 0.1.1 - October 23, 2014
--------------------------------

Bug Fixes:

- Fix erroneous warning when geofence action queue has been emptied and a new
  action is added. This fix also causes the new action to start running.
- Fix race condition reporting erroneous warning caused by multi-step actions
  are rapidly added to the queue.
- Fix `ClassCastException` caused if the AltBeacon `BeaconManager` is created
  prior to the Proximity Kit manager being instantiated.
- Uses distance calculations specific to the Android model
- Fix bug with RegionBootstrap setting background mode incorrectly on app
  launch


Version 0.1.0 - September 4, 2014
---------------------------------

- Requires JDK 1.7+
- Requires Android minimum SDK version 9+
- [AltBeacon.org](http://altbeacon.org/) support for monitoring beacon regions
  using the v1.0 specification
- Uses [AltBeacon/android-beacon-library](https://github.com/AltBeacon/android-beacon-library)
  version [2.0-beta6](https://github.com/AltBeacon/android-beacon-library/releases/tag/2.0-beta.6)
- Geofence support using [Google Play
  services](https://developer.android.com/google/play-services) 4.4+

Major technical focuses for this release were on adding geofence and AltBeacon
v1.0 spec support. Receiving notification of various events is provided by
registering as one or more a callbacks. These callbacks are defined by several
interfaces based on the activity and type of region.

- `ProximityKitMonitorNotifier`

  Notifies the app when the host device has come with-in or moved out-of
  proximity with any of the registered beacons.

- `ProximityKitRangeNotifier`

  Available while the host device is with-in a registered beacon regions.
  Provides the app with constant notifications about the device's relative
  proximity to those regions.

- `ProximityKitGeofenceNotifier`

  Notifies the app when the host device has come with-in or moved out-of
  any of the registered geofence regions.

- `ProximityKitSyncNotifier`

  Notifies the app after Proximity Kit has been synced with the cloud.
