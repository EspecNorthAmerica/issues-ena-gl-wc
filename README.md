# Issue Tracker for GL and web controller.

# Version history:

* 2026.0916.0 | GUI:4.1.2 | API:3.6.2 | PLC:2026.0713.1 | 9/16/2026
  * OS: Fix issue port forwarding native TCP communication protocols directly to chamber controller, ex: ModbusTCP.
* 2026.0915.0 | GUI:4.1.2 | API:3.6.1 | PLC:2026.0713.1 | 9/15/2026
  * OS: Fix missing mender identity script from differences in debian 11 and 13 mender-client.deb.
    Without this fix the OTA updates will not work, standalone uploads will work as is.
* 2026.0903.4 | GUI:4.1.1 | API:3.6.1 | PLC:2026.0713.1 | 9/3/2026
  * ALL: Several fixes to improve factory startup proceedure.
  * PLC: Fix the reported PLC version to show its actual version and not a copy of the system version.
* 2026.0831.5 | GUI:4.1.0 | API:3.6.0 | PLC:2026.0713.1 | 8/31/2026
  * ALL: Add a time based version numbering to fix ambigious multi project mess we had until now.
  * ALL: Add apprise notification method.
  * ALL: Add compatibility mode for RS232 communiation mode on the P300 vs GL (just use p300 mode).
  * ALL: Add back trace feature for T-Series
  * ALL: Add RS10 refirgerant support.
  * OS:  Migragte from Debian 11 to Debian 13
  * OS:  Move from xserver to wayland.
  * OS:  Fix handling of eth1.
  * API: Switch default email provider to use apprise with an espec account (o365 disabled smpt).
  * API: Migrate from Python 3.9 to 3.13
  * API: Migrate from pip/venv to uv.
  * API: Fix several logging issues.
  * API: Fixes for task queue shutdown handling (speeds up restarts).
  * API: Move 
  * API: SSE Fixes: frontend-settings deletes not working
  * API: Macro fixes, redis thread handling, period triggers, fix delay trigger.
  * API: Update hmtl report output plot tool, much easier to use.
  * GUI: Add support for all legacy controllers supported by API.
  * GUI: Add HASS screen for simple control/monitor/plot all on a single page.
  * GUI: Add top bar display mode which matches V3 gui.
  * GUI: Improved plot controls: ctrl+click plot area to perform measurements.
  * GUI: Improved plot controls: ctrl+click a legend item to isolate it.
  * GUI: Add a very early loading screen to display something ASAP on slow connections/HMI.
  * GUI: Add notification for server restarts.
  * GUI: Show program slots as busy when they are being altered (copy, move, delete, rename).
  * GUI: Rework loading screen to detail better detail where we may get stuck at.
  * GUI: Add humidity chart feature to constant and program screens.
  * GUI: on alarm, instead of re-directing to a the alarm page, display a very large modal (don't interrupt users current workflow).
  * GUI: Port macro editor from V3 GUI.
  * GUI: Port Network screen from V3 GUI.
  * GUI: On HMI add swipe back navigation.
  * GUI: General i18n fixes.
  * GUI: Fixes for mobile page views.
  * GUI: Performance improvements on active alarm list on chamber with huge alarm history.
  * GUI: Improve logging to help find issues quickly.
  * GUI: Refractor for mutliple "apps".
* GUI:4.0.8 | API:3.5.3 | PLC:2026.0519.1 | 05/19/2026
  * API: Fix json exports not actually containing any json.
* GUI:4.0.8 | API:3.5.2 | PLC:2026.0507.1 | 05/07/2026
  * PLC: Update to superheat control algorithm.
  * PLC: Fix rSelectSmooth algorithm to transition smoothly between values correctly (not really used).
  * PLC: Change Thermistor input to use an actively measured reference voltage.
  * PLC: Update Thermistor error handling to properly respond to errors.
  * API: Update default settings to setup the thermistor input for all models (unused on EP).
  * API: Handle replace floating point NaN values with null in json instead of returning invalid json.
  * API: Update default GL EP RefrigHighStage default superheat pids.
  * API: Adjust default CM201 discharge alarm temp to 120 (110 is to low).
  * API: Fix GN2 option not populating the correct time signal.
* GUI:4.0.8 | API:3.5.1 | PLC:2026.0429.1 | 04/29/2026
  * GUI/API: Adds support for keyprotect command/feature.
  * GUI: Updates for language files.
  * PLC: Fix EP LN2 handling when superheat takes effect.
* GUI:4.0.7 | API:3.5.0 | PLC:2026.0414.3 | 04/14/26
  * ALL: Add settings for when cascade refrigeration system is run in energy saver (APP.setup.rRefrigCascadeBelowSP, APP.setup.rRefrigCascadeBelowPV).
  * ALL: Unlink external/internal steam generator control logic from refrigeration base algorithm, uses xHumiSteamGenExternal now.
  * ALL: Allows saving of custom performance specs.
  * GUI: Update logging wrapper to handle the logging service failing.
  * GUI: Change the default audible alarm tone, and volume to help make HMI alarms noticable.
  * GUI: Add mechinism for controlling the hdmi display volume on the HMI (force it to 100%)
  * GUI: Adds performance specs for EGN models.
  * API: Ensure /sse does not refresh values too quickly by applying a min refresh type based on the default CACHE timeout of the controller.
  * API: Cleanup redundant logging.
  * API: ACCESS logger name covers all API calls, adds many request specific details for auditting and debugging.
  * API: Updates for EPX-4J and EPZ-4J from production.
  * API: Fix for redis not using monotonic expiration, (fixes caching issues).
  * API: Fix some frontend-settings killing the sse connection.
  * API: Fix invalid JWT returning a 403, now returns a 401 which causes the GUI app to log the user out.
  * API: Additional AUTH fixes due to invalid/expired JWT, SSE, and cache.
  * API: Merging in updates for T-Series for future release(s).
  * API: Move task queue memory leak detection from worker to manager (50% reduction in worker cpu load). Old method is still available for debugging as required.
  * API: Standardizing setting generation method, adds settings for EGN chambers.
  * PLC: Correct a edge case where low stage compressor could be on while high stage compressor is off.
  * PLC: Correct EP Pressure reset control order of operations.
  * PLC: Correct handling of coupled high/low pressure switches on EGN chambers.
* GUI:4.0.6 | API:3.4.6 | PLC:2026.0116.2 | 01/16/26
  * API: Update default settings for EPL-4J
  * API: Change codesys licensing Alarm to warning as it does not stop chamber operation immediately.
  * PLC: Fix low humidity option on platinous, previous update broke it.
  *  PLC: Fix WB/DB initial fill being cut shorter than intended.
* GUI:4.0.5 | API:3.4.5 | PLC:2026.0112.3 | 01/12/26
  * API: Fix humidity water supply alarm to warning as it does not stop the chamber.
  * PLC: Fix humidity water supply alarm handling to ensure steam heater is not enabled if there is no water in the duck pond on platinous chambers.
* GUI:4.0.4 | API:3.4.4 | PLC:2025.1222.2 | 12/22/25 (NOT SHIPPED)
  * API: Add a proper polling fallback system for /sse endpoint.
  * API: Add more channels to /sse endpoint for front end performance enhancements.
  * API: Fix automatic backtrace handling. Multiple report generation was possible, one per task worker.
  * API: Fix manual backtrace handling. Use the task engine instead of bespoke call method.
  * API: Fix timer showing constant mode 1 when constant mode 2/3 are selected.
  * API: Re-work application logging system into a database, better query.
  * API: Fix end of program handling, next program did not work correctly, and standby/constant where functional but caused timeout errors.
  * PLC: Add SSR maximum output power scaling factor, ie map 0-100% to a configurable 0-x%
  * PLC: Fix how Ti and Td of 0 are handled on PID loops. (DISABLED FOR NOW)
  * PLC: Adds R480A PT chart.
  * GUI: Performance enhancements by using central application state store(s) and syncing system status using /sse endpoint to update GUI only when needed (and as soon as possible). Until now this was only used for the /conditions end point, now it is used for all non setup needs.
  * GUI: Performance enhancements by refactoring some vue components using in large list rendering.
  * GUI: Performance enhancements by implementing virtual scrolling on more elements with large lists.
  * GUI: Snap scrolling is on virtual scroll elements has been re-worked. Previous method fought with virtual elements as the browser cannot snap to elements that do not exist while scrolling.
  * GUI: Fix missing error messages when api requests fail.
* GUI:4.0.3 | API:3.4.3 | ??? | 11/14/25
  * GUI: Fix custom grid rendering on mobile (home/mon2/mon3).
  * GUI: Implement chrome warning suggestions (Invalid icon, form helpers, etc).
  * GUI: Add trace searching to log viewer.
  * GUI: Prevent user from starting invalid programs, they need to open in editor to correct (auto corrections)
  * GUI: Fixes for service pages.
  * GUI: Updates to SSE handling for new RCF6902 format.
  * GUI: Better error handling on SSE connection, prevents multiple connections on errors.
  * GUI: Address issue where alarm screen may not show model/serial/series/name.
  * API: Add tracing to system logs.
  * API: Transition backtrace logs from xlsx reports to html reports. This is due to very bad performance of the xlsx routines.
  * API: Fix "MON?" and "PTC MON?" commands for GL.
  * API: Updates to SSE handling for new RCF6902 format. Adds more available channels/parts. SSE API is not considered stable and may change.
  * API: GL Model Settings Updates.
  * PLC: Fix for EGNX superheat control during humidity mode.
* GUI:4.0.2 | API:3.4.2 | ??? | 11/6/25
  * GUI: Fix possible race condition hen opening program editor. Existed before 4.0.1 but performance enhancements w/4.0.1 highlighted issue.
  * GUI: Add details to service pages.
  * GUI: Performance enhancements; Re-work pages composable to use pinia due to race condition on startup.
  * API: Fix update procedure w/ setting and/or config file changes.
* GUI:4.0.1 | API:3.4.1 | ??? | 11/5/25
  * GUI: Update default backtrace log views.
  * GUI: Performance enhancements, specifically targeting initial render times.
  * API: GL Model Setting Updates.
  * API: Task engine now cleans up Redis before starting. Prevents build up of tasks leading to system unresponsiveness.
  * PLC: Fix window heat.
  * PLC: Fix analog input range alarms tripping when in error state, Burn out detect handles error state seperately
* GUI:4.0.0 | API:3.4.0 | ??? | 10/31/25
  * Initial release
