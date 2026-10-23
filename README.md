# FabricDesk

Windows desktop app for day-to-day **Cisco ACI & Nexus Dashboard** operations. Multi-customer, runs locally on
your laptop. Built by Mushab Hamid.

**[Download the latest version](https://github.com/mushabasheera/fabricdesk-download/releases/latest)**. See [Download & install](#download--install).

Current version: **v0.5.0** (23 Oct 2026).

---

## Features

**Real fabric**: ✅ tested on a ACI 6.1 multi-site fabric.

| Menu | What it does | Real fabric |
|---|---|---|
| **Endpoint Locator** | Find an IP / MAC, or pull all endpoints (filter by tenant / VRF / EPG) -> leaf / port, VLAN, tenant, VRF, BD, EPG, ESG, last change | ✅ (endpoint lookup, also used by Contract Check and Request Triage) |
| **Contract Check** | Is `src -> dst:port` permitted by ACI policy? Checks direct contracts, contract interfaces, vzAny, preferred group, unenforced VRF, contracts in tenant common, cross-VRF scope, stretched / shadow objects, ext-EPG (longest prefix, flags the 0.0.0.0/0 catch-all), firewall boundary. Multi-site: checked in **both sites** (`PARTIAL` when only one site permits). Shows who is the *consumer* and the *provider*. Leave the port empty to see which ports the contract opens. Manual input or import a request form (CSV / XLSX) | ✅ |
| **Request Triage** (new) | Upload a connectivity request form (Excel / CSV) or paste requests from chat. The app finds the columns itself (multi-row headers, merged cells, "same as above" blank cells, IP ranges, hostnames, TCP/UDP, mixed port lists), checks every row against the fabric and sorts it into **New contract / Add ports / Partial / Firewall / Not in fabric / Manual / Open**. Flags rows the form marks as done that are not open yet, and lists the **changes needed** per consumer / provider. Column mappings can be saved per customer | ✅ (~800-row form, two sites, about 10 s) |
| **Ping** | Multi-threaded from the laptop (IP / CIDR / range / file input, live results, Stop button, log) or from a leaf (`iping -V tenant:vrf` over SSH) | ⚠️ laptop mode (needs no fabric API); leaf mode ⏳ |
| **Traceroute** | From the laptop (`tracert`) or from a leaf (`itraceroute -V tenant:vrf`) | ⚠️ laptop mode (needs no fabric API); leaf mode ⏳ |
| **API Runner** | **Templates** (an ordered list of API calls, e.g. "Configure Contract") -> **Library** (create API calls / import from Postman, assign to a template) -> **Environments** (Postman-style variables, several sets, Postman environment import). Run a template or selected API calls: choose environment + site, delay, CSV / XLSX test data (1 row = 1 iteration), **Preview iterations** before running, on error: stop / skip iteration / continue, result + error message **per iteration**. Login and tokens are handled by the app (no credentials needed in environments) | ⚠️ against NDO; APIC writes ⏳ |
| **Change Diff** | Snapshot the config (tenant / `uni/infra`) before and after a change, then compare (added / removed / modified, alert when objects were deleted) | ⏳ |
| **Policy Hygiene** (new) | Leftover and broken policy per site: broken contract references, contracts that permit nothing, half-used or unused contracts, unused filters, ESGs without selectors, EPGs without a bridge domain, empty BDs, unused VRFs. Severity + suggested action, Excel export | ✅ (findings checked by hand) |
| **Fault & Health** | Faults since a given time (by severity / keyword) + fabric, node and tenant health | ⏳ |
| **Leaf Health** | Per leaf: health score, CPU, memory, faults on that leaf, TCAM / capacity, port list, one line per port (status, speed, EPG / VLAN summary, SFP, CRC, errors, LLDP / CDP neighbor). **Click a port** -> details: reason the port is down, EPGs and VLANs on the port (encap and internal VLAN, trunk / access, through a port-channel / vPC, deployed or only configured), error counters, transceiver (PID / serial), optical Rx / Tx / temperature with warn / alarm thresholds (copper / UTP: *Not available*), LLDP / CDP | ✅ (CDP ⏳) |
| **Resource Check** | Free VLANs per pool / per IPG (AAEP -> domain -> pool), free ports per leaf, TCAM / capacity per leaf (Policy CAM, L2 / L3 endpoints, LPM, VLAN) + zoning rule count | ⚠️ TCAM / capacity only |
| **Settings** | Customer wizard (APIC sites, NDO / ND cluster, SSH to leaf, Test Connection, firewall boundary, request-form column mapping), Export / Import per customer (pick which credentials, API Runner library and form templates go into the file, and what to take on import), Audit Log, Check for updates | ✅ APIC and NDO login (3-node ND cluster) |

- Every result table can be exported to **CSV** and **Excel**.
- **Read-only towards the fabric.** Configuration changes only happen through the API Runner, after Preview
  iterations passes and you type the site name to confirm. Before writing to an APIC, the app can trigger a config
  snapshot automatically (roll back via Admin > Config Rollbacks).
- Several customers; pick the customer in the header. Each customer is set up once through the wizard.
- **Runs in the background**: while a check runs, its Run button becomes a **Stop** button. You can switch menus
  while a check is running: the left menu shows a spinner, you get a notification when it is done, and every page
  keeps its inputs and last result until you close the app.
- **Lost connection**: if the APIC / Nexus Dashboard cannot be reached (after one automatic retry), the check stops
  and a red alert tells you which controller and why, instead of running on and timing out.
- **Update notification**: when a new version is released, a banner appears inside the app with the release notes
  and a Download button.

---

## Status & limitations

Every feature is tested on real fabric (ACI 6.1, two sites) is shown in the **Real fabric** column of the feature table above.
Running checks in the background and the Stop button were tested on the real fabric too; the "lost connection"
alert was tested only with a simulated outage so far.

| Area | Missing / needs verification on a real fabric |
|---|---|
| Leaf Health | Tested on ACI 6.1 leaves only; CDP and optical thresholds may differ on other versions |
| Request Triage | Subnets and placeholders like `10.1.1.x` are not checked per host (Manual); IPs that only match a very broad external subnet are flagged "Low confidence"; firewall results rely on the boundary rules in the customer profile |
| Policy Hygiene | Findings are suggestions: check before deleting; clean up NDO-managed objects in NDO |
| Settings | Test Connection NDO / ND: reading the ND / NDO version is not verified yet |
| Endpoint Locator | ESG tag selectors, endpoint move history |
| Contract Check | Routing / L3Out is not checked (ALLOWED does not guarantee traffic flows), service graph / PBR, intersite vzAny per version |
| Ping / Traceroute | Leaf mode (SSH iping / itraceroute) not tested on a real leaf yet; no ND Connectivity Analysis yet |
| Fault & Health | No Nexus Dashboard anomalies yet |
| Resource Check | Access-policy interface selectors; TCAM numbers not yet compared side by side with the Capacity Dashboard GUI |
| Change Diff | No NDO template diff yet |
| API Runner | No automatic NDO backup before writes (take a manual backup first); Postman scripts (pre-request / test) are not executed |
| General | Windows 10 / 11 only. The exe is signed with a self-signed certificate, so Windows may still show a warning |

Found a bug or a number that differs from the APIC GUI? Let Mushab Hamid know. Error messages in the app can be
selected and copied.

---

## Download & install

**[Open the download page](https://github.com/mushabasheera/fabricdesk-download/releases/latest)** and download
`FabricDesk.exe` (~35 MB) under **Assets**. No installation needed: just run the exe.

### Notes

If Windows shows "Windows protected your PC" (SmartScreen) or *Unknown publisher*, click **More info** >
**Run anyway**. The warning appears because the app has not been downloaded many times yet, not because it is
harmful.

## Getting started

1. Double-click `FabricDesk.exe`. The window appears in about 3 seconds. Requires the Microsoft Edge WebView2
   Runtime (already part of Windows 10 / 11).
2. **Settings > Add customer** > enter the APIC IPs per site (several cluster IPs allowed, comma-separated) +
   username / password > **Test Connection**.
3. Pick a menu on the left.

All data stays on your own laptop (`%APPDATA%\FabricDesk`). Passwords are stored encrypted (Windows DPAPI) and are
only sent to the APIC / Nexus Dashboard you entered yourself. Error log: `%APPDATA%\FabricDesk\logs\fabricdesk.log`.

Sharing customers between engineers: **Settings > Export / Import** (`.fdbak` file, protected by a passphrase).
Per customer you choose which credentials go into the file (for example APIC DC1 only, or none) and whether the
API Runner library and form templates go with it. Tip: export **without** credentials if the APIC account is
personal.

## Updating

The app tells you when a new version is available (banner at the top, or **Settings > About > Check for
updates**). Click **Download**, then replace your old `FabricDesk.exe` with the new one. Your data and customers
are kept.
