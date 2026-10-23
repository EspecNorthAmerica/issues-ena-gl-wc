# ENA GL alarm code and I/O reference

The tables map ENA GL alarm codes to the PLC fault bits and configured devices that produce them. The API concatenates prefix `003` with a four-character code (for example, `003300A`); the frontend displays it as `003-300A` and resolves the generic English entry `-300A`. Indices and bit numbers below are zero based. ALM and WAR are frontend classifications, not proof of the actual PLC shutdown response.

The main table covers every code mapped by `Alarms` with `ena_gl_primary.yaml.j2`, including digital codes from the supplied ENA GL application settings. A code can have several sources: each source has its own row. The CSV also includes all English locale entries, retaining their event and cause text; entries without a trigger in this implementation are explicitly marked. Electrical contact polarity and PLC timing/enable logic cannot be derived from the Python decoder: a set alarm bit is the confirmed trigger here, not necessarily a raw input being electrically high.

CM101 is the first refrigeration circuit; CM201 is the second. HP/LP mean high/low pressure, Disc means discharge, SSH means solid-state humidity, and PPM means power phase monitor.

## Alarm codes and actual triggers

| Code | UI type | Frontend description | I/O or PLC trigger | Actual meaning | Conditions and mismatches |
| --- | --- | --- | --- | --- | --- |
| 003-0000 | WAR | INVALID CODESYS LICENSE | APP.alarms.wSystemFaults bit 0 | PLC application license fault. |  |
| 003-0001 | ALM | PLC NOT INITIALIZED | APP.alarms.wSystemFaults bit 1 | PLC initialization/configuration is still required. |  |
| 003-0002 | ALM | PLC IN STOP MODE | APP.alarms.wSystemFaults bit 7 | Alarm-handler reference error, according to the controller comment. | Code collision: frontend calls this PLC IN STOP MODE, but this system bit is commented as a reference error. |
| 003-0002 | ALM | PLC IN STOP MODE | APP.uliNowMS unchanged between successive successful checks, when nonzero | API infers the PLC has stopped advancing its clock. | Shared code with wSystemFaults bit 7. This is a clock comparison, not a read of an explicit PLC stopped flag. |
| 003-0010 | ALM | IO BOARD #0 COM ERROR | APP.alarms.wIoFaults bit 0 | PLC reports an I/O fault for board 0. | No individual field input is identified by this code. |
| 003-0011 | ALM | IO BOARD #1 COM ERROR | APP.alarms.wIoFaults bit 1 | PLC reports an I/O fault for board 1. | No individual field input is identified by this code. |
| 003-0012 | ALM | IO BOARD #2 COM ERROR | APP.alarms.wIoFaults bit 2 | PLC reports an I/O fault for board 2. | No individual field input is identified by this code. |
| 003-0013 | ALM | IO BOARD #3 COM ERROR | APP.alarms.wIoFaults bit 3 | PLC reports an I/O fault for board 3. | No individual field input is identified by this code. |
| 003-0014 | ALM | IO BOARD #4 COM ERROR | APP.alarms.wIoFaults bit 4 | PLC reports an I/O fault for board 4. | No individual field input is identified by this code. |
| 003-0015 | ALM | IO BOARD #5 COM ERROR | APP.alarms.wIoFaults bit 5 | PLC reports an I/O fault for board 5. | No individual field input is identified by this code. |
| 003-0016 | ALM | IO BOARD #6 COM ERROR | APP.alarms.wIoFaults bit 6 | PLC reports an I/O fault for board 6. | No individual field input is identified by this code. |
| 003-0017 | ALM | IO BOARD #7 COM ERROR | APP.alarms.wIoFaults bit 7 | PLC reports an I/O fault for board 7. | No individual field input is identified by this code. |
| 003-0018 | ALM | IO BOARD #8 COM ERROR | APP.alarms.wIoFaults bit 8 | PLC reports an I/O fault for board 8. | No individual field input is identified by this code. |
| 003-0019 | ALM | IO BOARD #9 COM ERROR | APP.alarms.wIoFaults bit 9 | PLC reports an I/O fault for board 9. | No individual field input is identified by this code. |
| 003-001A | ALM | IO BOARD #10 COM ERROR | APP.alarms.wIoFaults bit 10 | PLC reports an I/O fault for board 10. | No individual field input is identified by this code. |
| 003-001B | ALM | IO BOARD #11 COM ERROR | APP.alarms.wIoFaults bit 11 | PLC reports an I/O fault for board 11. | No individual field input is identified by this code. |
| 003-001C | ALM | IO BOARD #12 COM ERROR | APP.alarms.wIoFaults bit 12 | PLC reports an I/O fault for board 12. | No individual field input is identified by this code. |
| 003-001D | ALM | IO BOARD #13 COM ERROR | APP.alarms.wIoFaults bit 13 | PLC reports an I/O fault for board 13. | No individual field input is identified by this code. |
| 003-001E | ALM | IO BOARD #14 COM ERROR | APP.alarms.wIoFaults bit 14 | PLC reports an I/O fault for board 14. | No individual field input is identified by this code. |
| 003-001F | ALM | IO BOARD #15 COM ERROR | APP.alarms.wIoFaults bit 15 | PLC reports an I/O fault for board 15. | No individual field input is identified by this code. |
| 003-2000 | ALM | BOIL-DRY ALARM: HUM | IO.X[0,1] (X0,1 Humi Lim); APP.alarms.awDigInFaults[0] bit 1 | Humidity limiter input fault (Humi Lim). | Suppression depends on the selected model; see digital input masks below. |
| 003-2004 | WAR | WATER SUPPLY WARNING: HUM | APP.alarms.wSystemFaults bit 2 | Humidity water-fill timeout (xHumiFillTimout in the controller comment). |  |
| 003-2007 | WAR | WET BULB DRY WICK | APP.alarms.wSystemFaults bit 3 | Wet-bulb wick dry condition (xHumiWickDry). |  |
| 003-3000 | ALM | OVERHEATING ALARM | IO.X[0,0] (X0,0 PTP High); APP.alarms.awDigInFaults[0] bit 0 | Independent temperature protector high-limit trip (PTP High). | Suppression depends on the selected model; see digital input masks below. |
| 003-3002 | ALM | ABS HIGH LIMIT ALARM: TEMP | IO.AIN[0,1] (AIN0,1 TC1 Dry Bulb); APP.alarms.awAnlgLimMaxFaults[0] bit 1 | PLC reports the analog input above its configured maximum alarm limit. |  |
| 003-3002 | ALM | ABS HIGH LIMIT ALARM: TEMP | IO.AIN[0,3] (AIN0,3 TC3 Product); APP.alarms.awAnlgLimMaxFaults[0] bit 3 | PLC reports the analog input above its configured maximum alarm limit. |  |
| 003-3002 | ALM | ABS HIGH LIMIT ALARM: TEMP | APP.alarms.awLoopFaults[0] bit 0 (temperature loop) | Loop absolute high-limit fault; process value exceeded the absolute high limit. | Limit values come from the active run set. Humidity loop is exposed by this template on L/X models. |
| 003-3003 | WAR | ABS HIGH LIMIT WARNING: HUMID | APP.alarms.awLoopFaults[1] bit 0 (humidity loop) | Loop absolute high-limit fault; process value exceeded the absolute high limit. | Limit values come from the active run set. Humidity loop is exposed by this template on L/X models. |
| 003-3004 | ALM | ABS LOW LIMIT ALARM: TEMP | IO.AIN[0,1] (AIN0,1 TC1 Dry Bulb); APP.alarms.awAnlgLimMinFaults[0] bit 1 | PLC reports the analog input below its configured minimum alarm limit. |  |
| 003-3004 | ALM | ABS LOW LIMIT ALARM: TEMP | IO.AIN[0,3] (AIN0,3 TC3 Product); APP.alarms.awAnlgLimMinFaults[0] bit 3 | PLC reports the analog input below its configured minimum alarm limit. |  |
| 003-3004 | ALM | ABS LOW LIMIT ALARM: TEMP | APP.alarms.awLoopFaults[0] bit 1 (temperature loop) | Loop absolute low-limit fault; process value fell below the absolute low limit. | Limit values come from the active run set. Humidity loop is exposed by this template on L/X models. |
| 003-3005 | WAR | ABS LOW LIMIT WARNING: HUMID | APP.alarms.awLoopFaults[1] bit 1 (humidity loop) | Loop absolute low-limit fault; process value fell below the absolute low limit. | Limit values come from the active run set. Humidity loop is exposed by this template on L/X models. |
| 003-3006 | WAR | UPPER DEVIATION LIMIT WARNING: TEMP | APP.alarms.awLoopFaults[0] bit 2 (temperature loop) | Loop positive deviation-limit fault relative to the setpoint. | Limit values come from the active run set. Humidity loop is exposed by this template on L/X models. |
| 003-3007 | WAR | UPPER DEVIATION LIMIT WARNING: HUMID | APP.alarms.awLoopFaults[1] bit 2 (humidity loop) | Loop positive deviation-limit fault relative to the setpoint. | Limit values come from the active run set. Humidity loop is exposed by this template on L/X models. |
| 003-3008 | WAR | LOWER DEVIATION LIMIT WARNING: TEMP | APP.alarms.awLoopFaults[0] bit 3 (temperature loop) | Loop negative deviation-limit fault relative to the setpoint. | Limit values come from the active run set. Humidity loop is exposed by this template on L/X models. |
| 003-3009 | WAR | LOWER DEVIATION LIMIT WARNING: HUMID | APP.alarms.awLoopFaults[1] bit 3 (humidity loop) | Loop negative deviation-limit fault relative to the setpoint. | Limit values come from the active run set. Humidity loop is exposed by this template on L/X models. |
| 003-300A | ALM | SENSOR DISCONNECT ALARM: DRY-BULB | IO.AIN[0,1] (AIN0,1 TC1 Dry Bulb); APP.alarms.awAnlgOpenFaults[0] bit 1 | PLC reports an open/disconnected sensor input. | Open detection depends on xBurnDetect and chamber setup. |
| 003-300B | ALM | SENSOR DISCONNECT ALARM: WET-BULB | IO.AIN[0,2] (AIN0,2 TC2 Wet Bulb); APP.alarms.awAnlgOpenFaults[0] bit 2 | PLC reports an open/disconnected sensor input. | Open detection depends on xBurnDetect and chamber setup. |
| 003-300D | ALM | SENSOR DISCONNECT ALARM: HUMIDITY | IO.AIN[0,4] (AIN0,4 SSH); APP.alarms.awAnlgOpenFaults[0] bit 4 | PLC reports an open/disconnected sensor input. | Open detection depends on xBurnDetect and chamber setup. |
| 003-300F | ALM | SENSOR DISCON ALARM: PRODUCT TEMP | IO.AIN[0,3] (AIN0,3 TC3 Product); APP.alarms.awAnlgOpenFaults[0] bit 3 | PLC reports an open/disconnected sensor input. | Open detection depends on xBurnDetect and chamber setup. |
| 003-3012 | ALM | OVERCOOLING ALARM | IO.X[0,12] (X0,12 Overcool); APP.alarms.awDigInFaults[0] bit 12 | Independent overcool protection input fault. | Suppression depends on the selected model; see digital input masks below. |
| 003-3017 | ALM | DOOR OPEN | APP.alarms.wSystemFaults bit 6 | Door-open stop condition. | Door input is labeled X0,19 in DigInAlarms, but this code is emitted from the system fault bit; the PLC door logic is not present in these sources. |
| 003-3019 | WAR | DOOR OPEN (RUNNING) | APP.alarms.wSystemFaults bit 4 | Door warning condition. | Door input is labeled X0,19 in DigInAlarms, but this code is emitted from the system fault bit; the PLC door logic is not present in these sources. |
| 003-3020 | WAR | DOOR OPEN (PAUSE) | APP.alarms.wSystemFaults bit 5 | Door-open pause condition. | Door input is labeled X0,19 in DigInAlarms, but this code is emitted from the system fault bit; the PLC door logic is not present in these sources. |
| 003-4002 | ALM | POWER PHASE ALARM | IO.X[0,20] (X0,20 PPM); APP.alarms.awDigInFaults[0] bit 20 | Power phase monitor input fault (PPM). | Suppression depends on the selected model; see digital input masks below. |
| 003-4005 | ALM | EXTERNAL EQUIPMENT ALARM 1 | IO.X[0,6] (X0,6 CM101 Disc Temp); APP.alarms.awDigInFaults[0] bit 6 | CM101 discharge-temperature protection input fault. | Suppression depends on the selected model; see digital input masks below. All supplied defaults suppress this input. Frontend title describes external equipment, although this input is labeled CM101 Disc Temp. |
| 003-4005 | ALM | EXTERNAL EQUIPMENT ALARM 1 | IO.X[0,9] (X0,9 Ext Equip.); APP.alarms.awDigInFaults[0] bit 9 | External equipment fault input. | Suppression depends on the selected model; see digital input masks below. |
| 003-7000 | ALM | AIR CIRC ALARM | IO.X[0,4] (X0,4 Circ); APP.alarms.awDigInFaults[0] bit 4 | Circulation protection input fault (Circ). | Suppression depends on the selected model; see digital input masks below. |
| 003-8100 | ALM | REF-1 DISCON ALRM: DISCHRG LINE TEMP | IO.AIN[0,6] (AIN0,6 TC8 CM101 Discharge); APP.alarms.awAnlgOpenFaults[0] bit 6 | PLC reports an open/disconnected sensor input. | Open detection depends on xBurnDetect and chamber setup. |
| 003-8103 | ALM | REFRIG-1 DISCON ALARM: EVAP OUT TEMP | IO.AIN[0,5] (AIN0,5 TC6 CM101 Evap Out); APP.alarms.awAnlgOpenFaults[0] bit 5 | PLC reports an open/disconnected sensor input. | Open detection depends on xBurnDetect and chamber setup. |
| 003-8107 | ALM | REF-1 OUT OF RANGE ALRM: DISCHG TEMP | IO.AIN[0,6] (AIN0,6 TC8 CM101 Discharge); APP.alarms.awAnlgLimMinFaults[0] bit 6 OR APP.alarms.awAnlgLimMaxFaults[0] bit 6 | PLC reports the analog input outside its configured alarm range; either minimum or maximum fault produces this code. |  |
| 003-810A | ALM | REFRIG-1 OUT OF RANGE: EVAP OUT TEMP | IO.AIN[0,5] (AIN0,5 TC6 CM101 Evap Out); APP.alarms.awAnlgLimMinFaults[0] bit 5 OR APP.alarms.awAnlgLimMaxFaults[0] bit 5 | PLC reports the analog input outside its configured alarm range; either minimum or maximum fault produces this code. |  |
| 003-8115 | ALM | REFRIGERATOR-1 LOW PRESSURE ALARM | IO.X[0,11] (X0,11 CM101 LP); APP.alarms.awDigInFaults[0] bit 11 | CM101 low-pressure protection input fault. | Suppression depends on the selected model; see digital input masks below. |
| 003-8116 | ALM | REFRIG-1 COMPRESSOR CURRENT ALARM | IO.X[0,7] (X0,7 CM101 Current); APP.alarms.awDigInFaults[0] bit 7 | CM101 current protection input fault. | Suppression depends on the selected model; see digital input masks below. |
| 003-8117 | ALM | REFRIG-1 CONDENSER FAN CURRENT ALARM | IO.X[0,5] (X0,5 Condenser Fan); APP.alarms.awDigInFaults[0] bit 5 | Condenser fan protection input fault. | Suppression depends on the selected model; see digital input masks below. All supplied defaults suppress this input. |
| 003-8123 | ALM | REFRIG-1 (HIGH) HIGH PRESSURE ALARM | IO.X[0,2] (X0,2 CM101 HP); APP.alarms.awDigInFaults[0] bit 2 | CM101 high-pressure protection input fault. | Suppression depends on the selected model; see digital input masks below. |
| 003-812D | ALM | REFRIG-1 (LOW) HIGH PRESSURE ALARM | IO.X[0,3] (X0,3 CM201 HP); APP.alarms.awDigInFaults[0] bit 3 | CM201 high-pressure protection input fault. | Suppression depends on the selected model; see digital input masks below. |
| 003-812E | ALM | REFRIG-1 (LOW) LOW PRESSURE ALARM | IO.X[0,8] (X0,8 CM201 LP); APP.alarms.awDigInFaults[0] bit 8 | CM201 low-pressure protection input fault. | Suppression depends on the selected model; see digital input masks below. |
| 003-812F | ALM | REFRIG-1 (LOW) COMP CURRENT ALARM | IO.X[0,10] (X0,10 CM201 Current); APP.alarms.awDigInFaults[0] bit 10 | CM201 current protection input fault. | Suppression depends on the selected model; see digital input masks below. |
| 003-8132 | ALM | REFRIG-1 (LOW) DISCON: EVAP OUT TEMP | IO.AIN[0,7] (AIN0,7 TC10 CM201 Evap Out); APP.alarms.awAnlgOpenFaults[0] bit 7 | PLC reports an open/disconnected sensor input. | Open detection depends on xBurnDetect and chamber setup. |
| 003-8133 | ALM | REFRIG-1 (LOW) DISCON: DSCHRG LINE T | IO.AIN[0,8] (AIN0,8 TC12 CM201 Discharge); APP.alarms.awAnlgOpenFaults[0] bit 8 | PLC reports an open/disconnected sensor input. | Open detection depends on xBurnDetect and chamber setup. |
| 003-8135 | ALM | REF-1 (LOW) OUT OF RANGE: EVAP OUT T | IO.AIN[0,7] (AIN0,7 TC10 CM201 Evap Out); APP.alarms.awAnlgLimMinFaults[0] bit 7 OR APP.alarms.awAnlgLimMaxFaults[0] bit 7 | PLC reports the analog input outside its configured alarm range; either minimum or maximum fault produces this code. |  |
| 003-8136 | ALM | REF-1 (LOW) OUT OF RANGE: DISCHG TMP | IO.X[0,13] (X0,13 CM201 Disc Temp); APP.alarms.awDigInFaults[0] bit 13 | CM201 discharge-temperature protection input fault. | Suppression depends on the selected model; see digital input masks below. All supplied defaults suppress this input. |
| 003-8136 | ALM | REF-1 (LOW) OUT OF RANGE: DISCHG TMP | IO.AIN[0,8] (AIN0,8 TC12 CM201 Discharge); APP.alarms.awAnlgLimMinFaults[0] bit 8 OR APP.alarms.awAnlgLimMaxFaults[0] bit 8 | PLC reports the analog input outside its configured alarm range; either minimum or maximum fault produces this code. |  |
| 003-8141 | WAR | REF1 BOARD-1 SENSR DISCON WARN (DC1) | IO.AIN[0,9] (AIN0,9 CM101 Evap Pressure); APP.alarms.awAnlgOpenFaults[0] bit 9 | PLC reports an open/disconnected sensor input. | DC1 is configured as CM101 evaporator pressure. Open detection depends on xBurnDetect and chamber setup. |
| 003-8142 | WAR | REF1 BOARD-1 SENSR DISCON WARN (DC2) | IO.AIN[0,10] (AIN0,10 CM201 Evap Pressure); APP.alarms.awAnlgOpenFaults[0] bit 10 | PLC reports an open/disconnected sensor input. | DC2 is configured as CM201 evaporator pressure. Open detection depends on xBurnDetect and chamber setup. |
| 003-8143 | WAR | REF1 BOARD-1 SENSR DISCON WARN (DC3) | IO.AIN[0,11] (AIN0,11 Steam Gen Thermistor); APP.alarms.awAnlgOpenFaults[0] bit 11 | PLC reports an open/disconnected sensor input. | Actual configured device is the steam-generator thermistor on DC3; frontend refrigeration-board/pressure wording does not describe this device. Open detection depends on xBurnDetect and chamber setup. |
| 003-8159 | WAR | REF1 BRD-1 SNSR OUT RANGE WARN (DC1) | IO.AIN[0,9] (AIN0,9 CM101 Evap Pressure); APP.alarms.awAnlgLimMinFaults[0] bit 9 OR APP.alarms.awAnlgLimMaxFaults[0] bit 9 | PLC reports the analog input outside its configured alarm range; either minimum or maximum fault produces this code. | DC1 is configured as CM101 evaporator pressure. |
| 003-815A | WAR | REF1 BRD-1 SNSR OUT RANGE WARN (DC2) | IO.AIN[0,10] (AIN0,10 CM201 Evap Pressure); APP.alarms.awAnlgLimMinFaults[0] bit 10 OR APP.alarms.awAnlgLimMaxFaults[0] bit 10 | PLC reports the analog input outside its configured alarm range; either minimum or maximum fault produces this code. | DC2 is configured as CM201 evaporator pressure. |
| 003-815B | WAR | REF1 BRD-1 SNSR OUT RANGE WARN (DC3) | IO.AIN[0,11] (AIN0,11 Steam Gen Thermistor); APP.alarms.awAnlgLimMinFaults[0] bit 11 OR APP.alarms.awAnlgLimMaxFaults[0] bit 11 | PLC reports the analog input outside its configured alarm range; either minimum or maximum fault produces this code. | Actual configured device is the steam-generator thermistor on DC3; frontend refrigeration-board/pressure wording does not describe this device. |

## Digital input masks

The configured `mask: true` suppresses an input through `APP.setup.awAlarmDigInSuppress`; `mask: false` leaves it eligible for an alarm. The decoder itself reads `awDigInFaults` without checking the mask again. The selected chamber settings database can be edited, so these are shipped defaults rather than live wiring or live settings.

The table lists enabled inputs only. All other inputs are suppressed in that profile. The X0,6 and X0,13 discharge-temperature digital inputs and X0,5 condenser fan input are suppressed in every supplied profile; analog discharge-temperature alarms remain separate.

| Application settings files in ENA_GL | Inputs with mask false |
| --- | --- |
| EGNL12-4L, EGNL12-6L, EGNL12-7.5L, EGNL28-4L, EGNL28-6L, EGNL28-7.5L, EGNL4-5L, base-EGNL | X0,0, X0,1, X0,2, X0,4, X0,9, X0,11, X0,12, X0,20 |
| EGNL28-12L, EGNL28-15 | X0,0, X0,1, X0,2, X0,4, X0,7, X0,9, X0,11, X0,12, X0,20 |
| EGNU12-4L, EGNU12-6L, EGNU12-7.5L, EGNU28-4L, EGNU28-6L, EGNU28-7.5L, EGNU4-5L, base-EGNU | X0,0, X0,2, X0,4, X0,9, X0,11, X0,12, X0,20 |
| EGNU28-12L, EGNU28-15 | X0,0, X0,2, X0,4, X0,7, X0,9, X0,11, X0,12, X0,20 |
| EGNX12-28NWL | X0,0, X0,1, X0,2, X0,3, X0,4, X0,8, X0,11, X0,12, X0,20 |
| EGNX12-4L, EGNX12-6L, EGNX12-7.5L, EGNX28-4L, EGNX28-6L, EGNX28-7.5L, EGNX4-5L, base-EGN, base-EGNX | X0,0, X0,1, X0,2, X0,3, X0,4, X0,8, X0,9, X0,11, X0,12, X0,20 |
| EGNX28-12L, EGNX28-15, base | X0,0, X0,1, X0,2, X0,3, X0,4, X0,7, X0,8, X0,9, X0,10, X0,11, X0,12, X0,20 |
| EGNZ12-4L, EGNZ12-6L, EGNZ12-7.5L, EGNZ28-4L, EGNZ28-6L, EGNZ28-7.5L, EGNZ4-5L, base-EGNZ | X0,0, X0,2, X0,3, X0,4, X0,8, X0,9, X0,11, X0,12, X0,20 |
| EGNZ28-12L, EGNZ28-15 | X0,0, X0,2, X0,3, X0,4, X0,7, X0,8, X0,9, X0,10, X0,11, X0,12, X0,20 |
| EPL-2J, EPL-3J, EPL-4J, base-EPL | X0,0, X0,1, X0,2, X0,4, X0,9, X0,12 |
| EPU-2J, EPU-3J, EPU-4J, base-EPU | X0,0, X0,2, X0,4, X0,9, X0,12 |
| EPX-2J, EPX-3J, EPX-4J, base-EP, base-EPX | X0,0, X0,1, X0,2, X0,3, X0,4, X0,9, X0,12 |
| EPZ-2J, EPZ-3J, EPZ-4J, base-EPZ | X0,0, X0,2, X0,3, X0,4, X0,9, X0,12 |

## Analog channel configuration

Channel roles below come from `AnalogInputConfig` in `base.yaml`; the limit values and burn detection shown are the unmodified `EPX-2J.yaml` defaults. They are configuration values, not a reconstruction of the PLC comparator logic. Other models and service settings can differ. `rAlarmMin = rAlarmMax = 0` does not by itself establish whether a range check is enabled.

For a solid-state humidity option (`hmode` starts with S), the parent template disables wet-bulb open detection on channel 2 and enables it on channel 4. The product channel has burn detection off in the shown defaults; selecting PTCON enables the template's product-control permission, but this template does not explicitly override channel 3 `xBurnDetect`.

| Input | Configured device | Open code | Low or high code | EPX-2J min / max | EPX-2J xBurnDetect |
| --- | --- | --- | --- | --- | --- |
| IO.AIN[0,1] | AIN0,1 TC1 Dry Bulb | 003-300A | low: 003-3004, high: 003-3002 | -80 / 190 | true |
| IO.AIN[0,2] | AIN0,2 TC2 Wet Bulb | 003-300B | No numeric mapping | 0 / 0 | true |
| IO.AIN[0,3] | AIN0,3 TC3 Product | 003-300F | low: 003-3004, high: 003-3002 | -80 / 190 | false |
| IO.AIN[0,4] | AIN0,4 SSH | 003-300D | No numeric mapping | 0 / 0 | false |
| IO.AIN[0,5] | AIN0,5 TC6 CM101 Evap Out | 003-8103 | range: 003-810A | 0 / 0 | true |
| IO.AIN[0,6] | AIN0,6 TC8 CM101 Discharge | 003-8100 | range: 003-8107 | 0 / 120 | true |
| IO.AIN[0,7] | AIN0,7 TC10 CM201 Evap Out | 003-8132 | range: 003-8135 | 0 / 0 | true |
| IO.AIN[0,8] | AIN0,8 TC12 CM201 Discharge | 003-8133 | range: 003-8136 | 0 / 120 | true |
| IO.AIN[0,9] | AIN0,9 CM101 Evap Pressure | 003-8141 | range: 003-8159 | 0 / 0 | true |
| IO.AIN[0,10] | AIN0,10 CM201 Evap Pressure | 003-8142 | range: 003-815A | 0 / 0 | true |
| IO.AIN[0,11] | AIN0,11 Steam Gen Thermistor | 003-8143 | range: 003-815B | 0 / 115 | false |

## Decoder behavior and ambiguous codes

- `003-0002` can mean an unchanged PLC clock or `wSystemFaults` bit 7, whose source comment says alarm-handler reference error. The returned code alone cannot distinguish them.
- `003-3002` and `003-3004` can originate from either the temperature PID limit or analog limits on TC1 (dry bulb) or TC3 (product). The returned alarm list contains no source identifier and does not remove duplicate codes.
- `003-4005` maps both X0,9 external equipment and X0,6 CM101 discharge-temperature protection. X0,6 is suppressed in all supplied defaults.
- `003-8136` maps both the analog CM201 discharge-temperature range fault on AIN0,8 and digital discharge-temperature protection X0,13. X0,13 is suppressed in all supplied defaults.
- `003-8143` / `003-815B` describe generic refrigeration-board DC3 faults in the frontend, but AIN0,11 is configured as a steam-generator thermistor. Treat its range alarm as a thermistor/temperature channel fault, not an evaporator pressure fault.
- `003-0003` exists in the locale, but the PLC-unreachable emission path is commented out in `Alarms._plc_state`; the comment delegates that handling to the broker.
- Alarm output is suppressed while controller uptime is below `GL_NUISANCE_SUPPRESS_TIME`, whose current default is 90 seconds.
- Unmapped analog fault channels emit strings such as `003-IO.AIN[0,12]-open` rather than a numeric code. The AIN0,12 mapping is commented out in the template. Channels with only an `open` mapping also use these fallback strings for low/high faults.
- System fault bits beyond the eight mapped entries return `wSystemFaults.N`. Digital inputs with an empty configured alarm can return the bare prefix `003`; the decoder does not provide a useful numeric code for them. `_alarm_id_di` returning `None` is converted to the string `None` before the fallback check, so a missing lookup can return `003None` instead of the intended `X[board][bit]` fallback.

The PLC implementation is needed to confirm raw contact polarity, exact enabling conditions, delays, and which outputs are stopped by each fault.

## Frontend entries without a trigger in this mapping

These English labels are available to the frontend but have no numeric trigger in the inspected alarm decoder plus supplied application settings. Their locale event/cause text is included in the CSV. Their presence here does not establish that an ENA GL chamber emits them. Short keys such as `-0` are frontend category fallbacks rather than complete four-character codes.

| Locale key | UI type | Frontend description |
| --- | --- | --- |
| -0 | ALM | UNKNOWN SYSTEM ALARM |
| -0003 | ALM | PLC NOT CONNECTED |
| -1 | ALM | UNKNOWN HEATING ALARM |
| -1000 | ALM | HEATER ALARM |
| -2 | ALM | UNKNOWN HUMIDITY ALARM |
| -2001 | WAR | SCALE ACCUM WARNING: HUM |
| -2002 | ALM | SCALE ACCUM ALARM: HUM |
| -2003 | ALM | DRN ALARM: HUM |
| -2005 | ALM | WATER SUPPLY ALARM: HUM |
| -2006 | ALM | HUMIDIFIER ALARM |
| -3 | ALM | UNKNOWN AIR CIRCUIT ALRAM |
| -3001 | ALM | LOCAL A/C OVERHEAT |
| -300C | ALM | SENSOR DISCON ALRM: TEMP CTRLR (RTD) |
| -300E | WAR | SENSOR DISCON WARNING: PRODUCT TEMP |
| -3010 | WAR | SENSOR DISCON WARNING: PRODUCT TEMP |
| -3011 | ALM | SENSOR DISCON ALARM: PRODUCT TEMP |
| -3013 | ALM | DEHUMIDIFIER ALARM |
| -3014 | ALM | IN DISCONNECT: FROST-FREE REGEN TEMP |
| -3015 | WAR | SENSOR DISCONNECT WARNING: AREA TEMP |
| -3016 | ALM | SENSOR DISCONNECT ALARM: AREA TEMP |
| -3018 | WAR | DOOR OPEN |
| -4000 | WAR | CHECK HUMIDIFIER OPERATION |
| -4001 | ALM | OUTPUT CIRCUIT ALARM |
| -4003 | ALM | WORKER SECURITY |
| -4004 | ALM | EMERGENCY STOP SWITCH |
| -4006 | ALM | EXTERNAL EQUIPMENT ALARM 2 |
| -4007 | ALM | EXTERNAL EQUIPMENT ALARM 3 |
| -4008 | ALM | EXTERNAL EQUIPMENT ALARM 4 |
| -4009 | ALM | EXTERNAL EQUIPMENT ALARM 5 |
| -400A | WAR | EXTERNAL EQUIPMENT WARNING 1 |
| -400B | WAR | EXTERNAL EQUIPMENT WARNING 2 |
| -400C | WAR | EXTERNAL EQUIPMENT WARNING 3 |
| -400D | WAR | EXTERNAL EQUIPMENT WARNING 4 |
| -400E | WAR | EXTERNAL EQUIPMENT WARNING 5 |
| -400F | ALM | WATER LEAK ALARM |
| -7 | ALM | UNKNOWN VENTILATION SYSTEM ALARM |
| -7001 | ALM | AIR CIRC INVERTER ALARM |
| -8 | ALM | UNKNOWN REFRIGERATION ALARM |
| -8000 | ALM | REFRIGERATOR ALARM |
| -8001 | ALM | COOLING WATER ALARM: REFRIGERATOR |
| -8002 | ALM | REFRIGERATOR SETTING ALARM |
| -8101 | ALM | REFRIG-1 DISCON ALARM: CONDEN TEMP |
| -8102 | ALM | REFRIG-1 DISCON ALARM: EVAP IN TEMP |
| -8104 | ALM | REFRIG-1 DISCON ALRM: COMP SUCT TEMP |
| -8105 | ALM | REFRIG-1 DISCON ALRM: COMP SURF TEMP |
| -8106 | ALM | REF-1 DISCON ALARM: COOL BYPASS TEMP |
| -8108 | ALM | REF-1 OUT OF RANGE ALRM: CONDEN TEMP |
| -8109 | ALM | REFRIG-1 OUT OF RANGE: EVAP IN TEMP |
| -810B | ALM | REF-1 OUT OF RANGE: COMP SUCT TEMP |
| -810C | ALM | REF-1 OUT OF RANGE: COMP SURF TEMP |
| -810D | ALM | REF-1 OUT OF RANGE: COOL BYPS TEMP |
| -810E | ALM | REFRIG-1 DISCHARGE LINE TEMP ALARM |
| -810F | ALM | REF-1 COMP SURF TEMP ALARM (HIGH) |
| -8110 | ALM | REF-1 COMP SURF TEMP ALARM (LOW) |
| -8111 | ALM | REFRIG-1 COOLING BYPASS TEMP ALARM |
| -8112 | ALM | REFRIG-1 COMP SURFACE TEMP ALARM |
| -8113 | ALM | REFRIGERATOR-1 COMPRESSOR ALARM |
| -8114 | ALM | REFRIGERATOR-1 HIGH PRESSURE ALARM |
| -8118 | ALM | REFRIGERATOR-1 UNIT ALARM |
| -8119 | ALM | REFRIG-1 INVERTER COMMUNIC ALARM |
| -811A | WAR | REFRIG-1 LIQUID BACK WARNING |
| -811B | ALM | REFRIG-1 LIQUID BACK ALARM |
| -811C | ALM | REF-1 DISCON ALARM: EVAP IN TEMP 2 |
| -811D | ALM | REF-1 DISCON ALARM: EVAP OUT TEMP 2 |
| -811E | ALM | REF-1 OUT OF RANGE: EVAP IN TEMP 2 |
| -811F | ALM | REF-1 OUT OF RANGE: EVAP OUT TEMP 2 |
| -8120 | WAR | REFRIG-1 LIQUID BACK WARNING: COMP |
| -8121 | ALM | REFRIG-1 LIQUID BACK ALARM: COMP |
| -8122 | ALM | REF-1 (HIGH) COMP SURF TEMP ALARM |
| -8124 | ALM | REFRIG-1 (HIGH) LOW PRESSURE ALARM |
| -8125 | ALM | REFRIG-1 (HIGH) COMP CURRENT ALARM |
| -8126 | ALM | REFRIG-1 (HIGH) DISCON: EVAP IN TEMP |
| -8127 | ALM | REFRIG-1 (HIGH) DISCON: EVAP OUT TMP |
| -8128 | ALM | REF-1 (HIGH) OUT OF RANGE: EVAP IN T |
| -8129 | ALM | REF-1 (HIGH) OUT OF RNGE: EVAP OUT T |
| -812A | WAR | REF-1 (HIGH) LIQUID BACK WARN: COMP |
| -812B | ALM | REF-1 (HIGH) LIQUID BACK ALARM: COMP |
| -812C | ALM | REF-1 (LOW) COMP SURF TEMP ALARM |
| -8130 | ALM | REF-1 (LOW) DSCHRG LINE TEMP ALARM |
| -8131 | ALM | REFRIG-1 (LOW) DISCON: EVAP IN TEMP |
| -8134 | ALM | REF-1 (LOW) OUT OF RANGE: EVAP IN T |
| -8137 | WAR | REF-1 (LOW) LIQUID BACK WARN: COMP |
| -8138 | ALM | REF-1 (LOW) LIQUID BACK ALARM: COMP |
| -8139 | ALM | REF1 BOARD-1 SENSOR DISCON ALM (RTD) |
| -813A | ALM | REF1 BOARD-2 SENSOR DISCON ALM (RTD) |
| -813B | ALM | REF1 BOARD-3 SENSOR DISCON ALM (RTD) |
| -813C | WAR | REF1 BOARD-1 SENSR DISCON WARN (TC1) |
| -813D | WAR | REF1 BOARD-1 SENSR DISCON WARN (TC2) |
| -813E | WAR | REF1 BOARD-1 SENSR DISCON WARN (TC3) |
| -813F | WAR | REF1 BOARD-1 SENSR DISCON WARN (TC4) |
| -8140 | WAR | REF1 BOARD-1 SENSR DISCON WARN (TC5) |
| -8144 | WAR | REF1 BOARD-2 SENSR DISCON WARN (TC1) |
| -8145 | WAR | REF1 BOARD-2 SENSR DISCON WARN (TC2) |
| -8146 | WAR | REF1 BOARD-2 SENSR DISCON WARN (TC3) |
| -8147 | WAR | REF1 BOARD-2 SENSR DISCON WARN (TC4) |
| -8148 | WAR | REF1 BOARD-2 SENSR DISCON WARN (TC5) |
| -8149 | WAR | REF1 BOARD-2 SENSR DISCON WARN (DC1) |
| -814A | WAR | REF1 BOARD-2 SENSR DISCON WARN (DC2) |
| -814B | WAR | REF1 BOARD-2 SENSR DISCON WARN (DC3) |
| -814C | WAR | REF1 BOARD-3 SENSR DISCON WARN (TC1) |
| -814D | WAR | REF1 BOARD-3 SENSR DISCON WARN (TC2) |
| -814E | WAR | REF1 BOARD-3 SENSR DISCON WARN (TC3) |
| -814F | WAR | REF1 BOARD-3 SENSR DISCON WARN (TC4) |
| -8150 | WAR | REF1 BOARD-3 SENSR DISCON WARN (TC5) |
| -8151 | WAR | REF1 BOARD-3 SENSR DISCON WARN (DC1) |
| -8152 | WAR | REF1 BOARD-3 SENSR DISCON WARN (DC2) |
| -8153 | WAR | REF1 BOARD-3 SENSR DISCON WARN (DC3) |
| -8154 | WAR | REF1 BRD-1 SNSR OUT RANGE WARN (TC1) |
| -8155 | WAR | REF1 BRD-1 SNSR OUT RANGE WARN (TC2) |
| -8156 | WAR | REF1 BRD-1 SNSR OUT RANGE WARN (TC3) |
| -8157 | WAR | REF1 BRD-1 SNSR OUT RANGE WARN (TC4) |
| -8158 | WAR | REF1 BRD-1 SNSR OUT RANGE WARN (TC5) |
| -815C | WAR | REF1 BRD-2 SNSR OUT RANGE WARN (TC1) |
| -815D | WAR | REF1 BRD-2 SNSR OUT RANGE WARN (TC2) |
| -815E | WAR | REF1 BRD-2 SNSR OUT RANGE WARN (TC3) |
| -815F | WAR | REF1 BRD-2 SNSR OUT RANGE WARN (TC4) |
| -8160 | WAR | REF1 BRD-2 SNSR OUT RANGE WARN (TC5) |
| -8161 | WAR | REF1 BRD-2 SNSR OUT RANGE WARN (DC1) |
| -8162 | WAR | REF1 BRD-2 SNSR OUT RANGE WARN (DC2) |
| -8163 | WAR | REF1 BRD-2 SNSR OUT RANGE WARN (DC3) |
| -8164 | WAR | REF1 BRD-3 SNSR OUT RANGE WARN (TC1) |
| -8165 | WAR | REF1 BRD-3 SNSR OUT RANGE WARN (TC2) |
| -8166 | WAR | REF1 BRD-3 SNSR OUT RANGE WARN (TC3) |
| -8167 | WAR | REF1 BRD-3 SNSR OUT RANGE WARN (TC4) |
| -8168 | WAR | REF1 BRD-3 SNSR OUT RANGE WARN (TC5) |
| -8169 | WAR | REF1 BRD-3 SNSR OUT RANGE WARN (DC1) |
| -816A | WAR | REF1 BRD-3 SNSR OUT RANGE WARN (DC2) |
| -816B | WAR | REF1 BRD-3 SNSR OUT RANGE WARN (DC3) |
| -816C | WAR | 冷凍機1 圧縮機過熱不足警告 |
| -816D | ALM | 冷凍機1 圧縮機過冷却異常 |
| -816E | WAR | 冷凍機1 圧縮機過冷却警告 |
| -816F | WAR | 冷凍機1高圧圧力警告 |
| -8170 | ALM | 冷凍機1(低) 圧縮機表面高温異常 |
| -8171 | ALM | 冷凍機1(低) 圧縮機表面低温異常 |
| -8172 | ALM | 冷凍機1(低) 圧縮機過冷却異常 |
| -8173 | WAR | 冷凍機1(低) 圧縮機過冷却警告 |
| -8174 | ALM | 冷凍機1 過冷却入口温度断線異常 |
| -8175 | ALM | 冷凍機1 過冷却出口温度断線異常 |
| -8176 | WAR | 冷凍機1(低) 高圧圧力警告 |
| -8177 | ALM | 冷凍機1 GINJ入口温度断線異常 |
| -8178 | ALM | 冷凍機1 GINJ出口温度断線異常 |
| -8179 | ALM | 冷凍機1 凝縮圧力センサー異常 |
| -817A | ALM | 冷凍機1 蒸発圧力センサー異常 |
| -817B | ALM | 冷凍機1 吸込圧力センサー異常 |
| -817C | ALM | 冷凍機1 高温側表面温度断線 |
| -817D | ALM | 冷凍機1 低温側表面温度断線 |
| -8300 | ALM | REF-2 DISCON ALRM: DISCHRG LINE TEMP |
| -8301 | ALM | REFRIG-2 DISCON ALARM: CONDEN TEMP |
| -8302 | ALM | REFRIG-2 DISCON ALARM: EVAP IN TEMP |
| -8303 | ALM | REFRIG-2 DISCON ALARM: EVAP OUT TEMP |
| -8304 | ALM | REFRIG-2 DISCON ALRM: COMP SUCT TEMP |
| -8305 | ALM | REFRIG-2 DISCON ALRM: COMP SURF TEMP |
| -8306 | ALM | REF-2 DISCON ALARM: COOL BYPASS TEMP |
| -8307 | ALM | REF-2 OUT OF RANGE ALRM: DISCHG TEMP |
| -8308 | ALM | REF-2 OUT OF RANGE ALRM: CONDEN TEMP |
| -8309 | ALM | REFRIG-2 OUT OF RANGE: EVAP IN TEMP |
| -830A | ALM | REFRIG-2 OUT OF RANGE: EVAP OUT TEMP |
| -830B | ALM | REF-2 OUT OF RANGE: COMP SUCT TEMP |
| -830C | ALM | REF-2 OUT OF RANGE: COMP SURF TEMP |
| -830D | ALM | REF-2 OUT OF RANGE: COOL BYPS TEMP |
| -830E | ALM | REFRIG-2 DISCHARGE LINE TEMP ALARM |
| -830F | ALM | REF-2 COMP SURF TEMP ALARM (HIGH) |
| -8310 | ALM | REF-2 COMP SURF TEMP ALARM (LOW) |
| -8311 | ALM | REFRIG-2 COOLING BYPASS TEMP ALARM |
| -8312 | ALM | REFRIG-2 COMP SURFACE TEMP ALARM |
| -8313 | ALM | REFRIGERATOR-2 COMPRESSOR ALARM |
| -8314 | ALM | REFRIGERATOR-2 HIGH PRESSURE ALARM |
| -8315 | ALM | REFRIGERATOR-2 LOW PRESSURE ALARM |
| -8316 | ALM | REFRIG-2 COMPRESSOR CURRENT ALARM |
| -8317 | ALM | REFRIG-2 CONDENSER FAN CURRENT ALARM |
| -8318 | ALM | REFRIGERATOR-2 UNIT ALARM |
| -8319 | ALM | REFRIG-2 INVERTER COMMUNIC ALARM |
| -831A | WAR | REFRIG-2 LIQUID BACK WARNING |
| -831B | ALM | REFRIG-2 LIQUID BACK ALARM |
| -831C | ALM | REF-2 DISCON ALARM: EVAP IN TEMP 2 |
| -831D | ALM | REF-2 DISCON ALARM: EVAP OUT TEMP 2 |
| -831E | ALM | REF-2 OUT OF RANGE: EVAP IN TEMP 2 |
| -831F | ALM | REF-2 OUT OF RANGE: EVAP OUT TEMP 2 |
| -8320 | WAR | REFRIG-2 LIQUID BACK WARNING: COMP |
| -8321 | ALM | REFRIG-2 LIQUID BACK ALARM: COMP |
| -8322 | ALM | REF-2 (HIGH) COMP SURF TEMP ALARM |
| -8323 | ALM | REFRIG-2 (HIGH) HIGH PRESSURE ALARM |
| -8324 | ALM | REFRIG-2 (HIGH) LOW PRESSURE ALARM |
| -8325 | ALM | REFRIG-2 (HIGH) COMP CURRENT ALARM |
| -8326 | ALM | REFRIG-2 (HIGH) DISCON: EVAP IN TEMP |
| -8327 | ALM | REFRIG-2 (HIGH) DISCON: EVAP OUT TMP |
| -8328 | ALM | REF-2 (HIGH) OUT OF RANGE: EVAP IN T |
| -8329 | ALM | REF-2 (HIGH) OUT OF RNGE: EVAP OUT T |
| -832A | WAR | REF-2 (HIGH) LIQUID BACK WARN: COMP |
| -832B | ALM | REF-2 (HIGH) LIQUID BACK ALARM: COMP |
| -832C | ALM | REF-2 (LOW) COMP SURF TEMP ALARM |
| -832D | ALM | REFRIG-2 (LOW) HIGH PRESSURE ALARM |
| -832E | ALM | REFRIG-2 (LOW) LOW PRESSURE ALARM |
| -832F | ALM | REFRIG-2 (LOW) COMP CURRENT ALARM |
| -8330 | ALM | REF-2 (LOW) DSCHRG LINE TEMP ALARM |
| -8331 | ALM | REFRIG-2 (LOW) DISCON: EVAP IN TEMP |
| -8332 | ALM | REFRIG-2 (LOW) DISCON: EVAP OUT TEMP |
| -8333 | ALM | REFRIG-2 (LOW) DISCON: DSCHRG LINE T |
| -8334 | ALM | REF-2 (LOW) OUT OF RANGE: EVAP IN T |
| -8335 | ALM | REF-2 (LOW) OUT OF RANGE: EVAP OUT T |
| -8336 | ALM | REF-2 (LOW) OUT OF RANGE: DISCHG TMP |
| -8337 | WAR | REF-2 (LOW) LIQUID BACK WARN: COMP |
| -8338 | ALM | REF-2 (LOW) LIQUID BACK ALARM: COMP |
| -8339 | ALM | REF2 BOARD-1 SENSOR DISCON ALM (RTD) |
| -833A | ALM | REF2 BOARD-2 SENSOR DISCON ALM (RTD) |
| -833B | ALM | REF2 BOARD-3 SENSOR DISCON ALM (RTD) |
| -833C | WAR | REF2 BOARD-1 SENSR DISCON WARN (TC1) |
| -833D | WAR | REF2 BOARD-1 SENSR DISCON WARN (TC2) |
| -833E | WAR | REF2 BOARD-1 SENSR DISCON WARN (TC3) |
| -833F | WAR | REF2 BOARD-1 SENSR DISCON WARN (TC4) |
| -8340 | WAR | REF2 BOARD-1 SENSR DISCON WARN (TC5) |
| -8341 | WAR | REF2 BOARD-1 SENSR DISCON WARN (DC1) |
| -8342 | WAR | REF2 BOARD-1 SENSR DISCON WARN (DC2) |
| -8343 | WAR | REF2 BOARD-1 SENSR DISCON WARN (DC3) |
| -8344 | WAR | REF2 BOARD-2 SENSR DISCON WARN (TC1) |
| -8345 | WAR | REF2 BOARD-2 SENSR DISCON WARN (TC2) |
| -8346 | WAR | REF2 BOARD-2 SENSR DISCON WARN (TC3) |
| -8347 | WAR | REF2 BOARD-2 SENSR DISCON WARN (TC4) |
| -8348 | WAR | REF2 BOARD-2 SENSR DISCON WARN (TC5) |
| -8349 | WAR | REF2 BOARD-2 SENSR DISCON WARN (DC1) |
| -834A | WAR | REF2 BOARD-2 SENSR DISCON WARN (DC2) |
| -834B | WAR | REF2 BOARD-2 SENSR DISCON WARN (DC3) |
| -834C | WAR | REF2 BOARD-3 SENSR DISCON WARN (TC1) |
| -834D | WAR | REF2 BOARD-3 SENSR DISCON WARN (TC2) |
| -834E | WAR | REF2 BOARD-3 SENSR DISCON WARN (TC3) |
| -834F | WAR | REF2 BOARD-3 SENSR DISCON WARN (TC4) |
| -8350 | WAR | REF2 BOARD-3 SENSR DISCON WARN (TC5) |
| -8351 | WAR | REF2 BOARD-3 SENSR DISCON WARN (DC1) |
| -8352 | WAR | REF2 BOARD-3 SENSR DISCON WARN (DC2) |
| -8353 | WAR | REF2 BOARD-3 SENSR DISCON WARN (DC3) |
| -8354 | WAR | REF2 BRD-1 SNSR OUT RANGE WARN (TC1) |
| -8355 | WAR | REF2 BRD-1 SNSR OUT RANGE WARN (TC2) |
| -8356 | WAR | REF2 BRD-1 SNSR OUT RANGE WARN (TC3) |
| -8357 | WAR | REF2 BRD-1 SNSR OUT RANGE WARN (TC4) |
| -8358 | WAR | REF2 BRD-1 SNSR OUT RANGE WARN (TC5) |
| -8359 | WAR | REF2 BRD-1 SNSR OUT RANGE WARN (DC1) |
| -835A | WAR | REF2 BRD-1 SNSR OUT RANGE WARN (DC2) |
| -835B | WAR | REF2 BRD-1 SNSR OUT RANGE WARN (DC3) |
| -835C | WAR | REF2 BRD-2 SNSR OUT RANGE WARN (TC1) |
| -835D | WAR | REF2 BRD-2 SNSR OUT RANGE WARN (TC2) |
| -835E | WAR | REF2 BRD-2 SNSR OUT RANGE WARN (TC3) |
| -835F | WAR | REF2 BRD-2 SNSR OUT RANGE WARN (TC4) |
| -8360 | WAR | REF2 BRD-2 SNSR OUT RANGE WARN (TC5) |
| -8361 | WAR | REF2 BRD-2 SNSR OUT RANGE WARN (DC1) |
| -8362 | WAR | REF2 BRD-2 SNSR OUT RANGE WARN (DC2) |
| -8363 | WAR | REF2 BRD-2 SNSR OUT RANGE WARN (DC3) |
| -8364 | WAR | REF2 BRD-3 SNSR OUT RANGE WARN (TC1) |
| -8365 | WAR | REF2 BRD-3 SNSR OUT RANGE WARN (TC2) |
| -8366 | WAR | REF2 BRD-3 SNSR OUT RANGE WARN (TC3) |
| -8367 | WAR | REF2 BRD-3 SNSR OUT RANGE WARN (TC4) |
| -8368 | WAR | REF2 BRD-3 SNSR OUT RANGE WARN (TC5) |
| -8369 | WAR | REF2 BRD-3 SNSR OUT RANGE WARN (DC1) |
| -836A | WAR | REF2 BRD-3 SNSR OUT RANGE WARN (DC2) |
| -836B | WAR | REF2 BRD-3 SNSR OUT RANGE WARN (DC3) |

## Sources

- [Alarm configuration and analog/PID mappings](conf_templates/parents/ena_gl_primary.yaml.j2), `conditions.active_alarms` and initialization overrides.
- [Alarm decoding](especapi/chamber/controllers/espec/ena_gl/alarms.py), `Alarms`.
- [Digital alarm lookup and suppression conversion](especapi/chamber/controllers/espec/ena_gl/database.py), `digital_alarm_codes` and `to_plc_dict`.
- [ENA GL application settings](conf_templates/application_settings/ENA_GL/), including `base.yaml`, model-specific `DigInAlarms`, and `AnalogInputConfig`.
- [English frontend alarm descriptions](/home/mmetzler/projects/ew-fe-gl/src/locales/en/__glAlarmCodes.yaml).
- [Frontend alarm code formatting](/home/mmetzler/projects/ew-fe-gl/src/composables/useAlarmDecoder.ts).
- [Boot suppression default](especapi/config.py), `GL_NUISANCE_SUPPRESS_TIME`.

Companion data: [ENA_GL_ALARM_MAPPING.csv](ENA_GL_ALARM_MAPPING.csv).
