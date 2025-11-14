# LibreNMS Custom Alert Templates

This repository contains custom HTML alert templates for LibreNMS network monitoring system. These templates provide detailed, formatted email notifications for various network events and conditions.

## Overview

LibreNMS uses these templates to format alert notifications sent via email or other transport methods. Each template is designed for specific alert types and includes conditional formatting, device details, troubleshooting recommendations, and visual indicators (color-coded status markers and progress bars).

## Quick Reference

| Template File | Alert Rule(s) | Severity | Purpose |
|--------------|---------------|----------|---------|
| [Default Alert Template (HTML).md](#default-alert-template) | Multiple general alerts | Variable | Generic alert template for sensor limits, services, wireless sensors, and ZFS errors |
| [Device Down - No ICMP Response (HTML).md](#device-down---no-icmp-response) | Device Down! Due to no ICMP response | Critical | Device unreachable via ICMP ping |
| [Device Down - SNMP Unreachable (HTML).md](#device-down---snmp-unreachable) | Device Down (SNMP unreachable) | Critical | Device SNMP agent not responding |
| [Device Rebooted (HTML).md](#device-rebooted) | Device rebooted | Warning | Device uptime has reset |
| [Ping Latency - Critical (HTML).md](#ping-latency---critical) | Ping Latency (Critical) | Critical | Ping latency exceeds 10ms |
| [Ping Latency - Warning (HTML).md](#ping-latency---warning) | Ping Latency (Warning) | Warning | Ping latency between 5-10ms |
| [Port Status Up/Down (HTML).md](#port-status-updown) | Port status up/down | Critical | Network port operational status change |
| [Port Utilization - Critical (HTML).md](#port-utilization---critical) | Port utilisation over 95% | Critical | Port bandwidth usage above 95% |
| [Port Utilization - Warning (HTML).md](#port-utilization---warning) | Port Utilization between 80% - 95% | Warning | Port bandwidth usage between 80-95% |
| [Storage capacity above 90% (HTTP).md](#storage-capacity-above-90) | Storage Capacity >= 90% | Critical | Filesystem usage at or above 90% |

> See [Alert Template Assignments.png](Alert%20Template%20Assignments.png) for the visual mapping of templates to alert rules in LibreNMS.

---

## Template Details

### Default Alert Template

**File:** `Default Alert Template (HTML).md`

**Assigned to:**
- Sensor over limit - Check Device Health Settings
- Sensor under limit - Check Device Health Settings
- Service up/down
- State Sensor Critical
- Wireless Sensor over limit
- Wireless Sensor under limit
- ZFS has one or more pool DEGRADED, FAULTED, UNAVAIL, REMOVED, or other
- ZFS L2 errors present

**Description:**
A versatile HTML template serving as the catch-all for various general alerts. Features clean table-based layout with color-coded state markers (red for alerts, gold for acknowledged, green for recovered). Displays comprehensive device information including hostname, IP, location, OS, hardware, and uptime. Uses conditional formatting based on alert state and severity level.

**Key Features:**
- Color-coded state markers and row backgrounds
- Dynamic header showing alert severity or acknowledgment status
- Three main sections: Alert Details, Device Details, and Alert Information
- Minimal formatting suitable for multiple alert types
- Links to device page in LibreNMS (http://nms.cabnetworks.ca:8000)

---

### Device Down - No ICMP Response

**File:** `Device Down - No ICMP Response (HTML).md`

**Assigned to:** Device Down! Due to no ICMP response

**Description:**
Specialized template for critical device outages when a device stops responding to ICMP ping requests. Provides detailed outage information and comprehensive troubleshooting guidance. The template differentiates between active outages and recoveries with distinct messaging and visual indicators.

**Key Features:**
- Prominent alert/recovery messaging boxes with color-coded borders
- Outage duration tracking and timestamp information
- Context-aware messaging explaining potential causes (power off, network interruption, firewall blocking)
- Comprehensive recommended actions list including:
  - Device power status verification
  - Physical connection checks
  - Network path testing
  - Switch/router port verification
  - Firewall rule review
  - Scheduled maintenance checks
  - Physical inspection recommendations
- Full device details section

---

### Device Down - SNMP Unreachable

**File:** `Device Down - SNMP Unreachable (HTML).md`

**Assigned to:** Device Down (SNMP unreachable)

**Description:**
Template for alerts when the SNMP agent on a device becomes unreachable. Similar in structure to the ICMP response template but tailored for SNMP-specific troubleshooting. Critical for identifying devices that may still be reachable but have SNMP service issues.

**Key Features:**
- SNMP-specific outage messaging
- Outage duration and alert timestamp
- Tailored recommended actions for SNMP troubleshooting
- Device details and alert metadata
- Visual distinction between alert and recovery states

---

### Device Rebooted

**File:** `Device Rebooted (HTML).md`

**Assigned to:** Device rebooted

**Description:**
Informational template that triggers when a device's uptime counter resets, indicating a reboot or power cycle. Useful for tracking both planned maintenance and unexpected restarts. Helps differentiate between authorized maintenance windows and potential hardware issues.

**Key Features:**
- Current uptime display in alert
- Reboot detection timestamp
- Warning-level severity (not critical)
- Context boxes explaining potential causes (planned maintenance vs. unexpected restart)
- Recommended actions including:
  - Planned maintenance verification
  - System log review
  - Update check (automatic updates)
  - Stability monitoring
  - Service verification
  - Power issue investigation
  - Event documentation

---

### Ping Latency - Critical

**File:** `Ping Latency - Critical (HTML).md`

**Assigned to:** Ping Latency (Critical)

**Description:**
Monitors and alerts on elevated ping response times exceeding 10ms. Features visual latency indicators with color-coded progress bars and detailed threshold information. Critical for identifying network performance degradation before it impacts users.

**Key Features:**
- Real-time latency display with color coding:
  - Excellent: <1ms (blue)
  - Good: 1-5ms (green)
  - Warning: 5-10ms (orange)
  - Critical: >10ms (red)
- Visual progress bar showing current latency
- Latency threshold reference table
- Comprehensive troubleshooting recommendations:
  - Network path analysis (traceroute)
  - Bandwidth saturation checks
  - Device CPU/memory monitoring
  - Routing issue verification
  - Multi-source testing
  - QoS configuration review
  - ISP status checks
  - Pattern monitoring
- Note about packet loss monitoring being separate

---

### Ping Latency - Warning

**File:** `Ping Latency - Warning (HTML).md`

**Assigned to:** Ping Latency (Warning)

**Description:**
Warning-level template for ping latency between 5-10ms. Provides early warning before conditions become critical. Uses similar structure to the critical template but with orange/amber color scheme instead of red.

**Key Features:**
- Same latency visualization as critical template
- Warning-level color scheme (orange instead of red)
- Detailed latency metrics and progress bars
- Threshold reference guide
- Proactive troubleshooting steps
- Early intervention recommendations

---

### Port Status Up/Down

**File:** `Port Status Up/Port Status Up_Down (HTML).md`

**Assigned to:** Port status up/down

**Description:**
Monitors network port operational status changes (up/down transitions). Essential for tracking physical connectivity issues, cable failures, or administrative changes. Intelligently filters to show only the port(s) that triggered the alert.

**Key Features:**
- Port-specific status badges (UP/DOWN)
- Distinction between operational and administrative status
- Detailed port information:
  - Interface name and description
  - Operational status (up/down)
  - Administrative status (enabled/disabled)
  - Port speed (Gbps/Mbps/Kbps)
  - Port type
  - MAC address
  - Last status change time
- Context-aware recommended actions:
  - Different guidance for administratively disabled ports
  - Physical connectivity troubleshooting
  - Cable integrity checks
  - Connected device verification
  - Port security and auto-negotiation checks
- Smart port filtering (shows only affected ports)

---

### Port Utilization - Critical

**File:** `Port Utilization - Critical (HTML).md`

**Assigned to:** Port utilisation over 95%

**Description:**
Critical alert for network ports exceeding 95% bandwidth utilization. Provides detailed per-port statistics with inbound and outbound traffic analysis. Features visual progress bars and comprehensive traffic rate information to help identify bandwidth bottlenecks.

**Key Features:**
- Dual threshold awareness (80-95% warning, >95% critical)
- Per-port utilization details showing:
  - Inbound and outbound utilization percentages
  - Color-coded progress bars
  - Current traffic rates (Gbps/Mbps/Kbps/bps)
  - Port speed and description
- Summary of all affected ports in alert box
- Real-time traffic rate calculations (converted from bytes to bits)
- Utilization reference guide (Normal <79%, Warning 80-95%, Critical >95%)
- Comprehensive capacity planning recommendations:
  - Traffic source identification
  - Bandwidth-heavy process detection
  - QoS policy review
  - Traffic pattern analysis
  - Link upgrade planning
  - Network loop detection
  - Port configuration verification
  - Traffic shaping implementation

---

### Port Utilization - Warning

**File:** `Port Utilization - Warning (HTML).md`

**Assigned to:** Port Utilization between 80% - 95%

**Description:**
Warning-level template for ports approaching capacity (80-95% utilization). Provides early warning before reaching critical levels. Uses identical structure to the critical template but with warning-level color scheme and messaging.

**Key Features:**
- Same comprehensive port statistics as critical template
- Warning color scheme (orange instead of red)
- Dual-direction traffic monitoring (in/out)
- Visual progress bars for both directions
- Traffic rate conversion and display
- Proactive capacity planning guidance
- Helps prevent reaching critical levels

---

### Storage Capacity Above 90%

**File:** `Storage capacity above 90% (HTTP).md`

**Assigned to:** Storage Capacity >= 90%

**Description:**
Alerts when filesystem usage reaches or exceeds 90% capacity. Displays detailed storage metrics including used/free space calculations and visual progress bars. Critical for preventing out-of-space conditions that could cause service disruptions.

**Key Features:**
- Filesystem and mount point identification
- Current usage percentage with color coding:
  - Normal: <75% (green)
  - Warning: 75-90% (orange)
  - Critical: >=90% (red)
- Visual progress bar showing capacity
- Detailed space calculations:
  - Total size
  - Used space
  - Free space
  - All displayed in appropriate units (TB/GB/MB/KB/bytes)
- Recommended cleanup actions:
  - Large file investigation
  - Log rotation checks
  - Application cache review
  - Storage expansion planning
  - Backup process verification

---

## Template Structure

All templates follow a consistent structure:

### 1. Alert Title Section
```text
Dynamic title with emoji indicators and device hostname
Example: =¨ ALERT: DEVICE-NAME - CRITICAL - Alert Description
```

### 2. Recovery Title Section
```text
Alternative title for recovered/cleared alerts
Example:  RECOVERED: DEVICE-NAME - Alert Description
```

### 3. HTML Template Section
Complete HTML email template with:
- **CSS Styling**: Embedded styles for consistent formatting
- **State Markers**: Color-coded vertical bars (red/orange/green)
- **Header Section**: Alert state and severity
- **Details Sections**: Organized information blocks
- **Device Details**: Standard device information
- **Recommended Actions**: Troubleshooting steps (shown during active alerts)
- **Alert Metadata**: Rule name, severity, unique ID, timestamp

### Common Template Elements

#### Color Scheme
- **Critical/Alert**: Red (#bd1e1e)
- **Warning**: Orange (#ff9800)
- **Acknowledged**: Gold (rgb(187, 135, 4))
- **Recovered**: Green (rgb(0, 92, 0))
- **Info**: Blue (#2196f3)

#### Device Information
All templates include:
- Hostname and System Name (with LibreNMS link)
- Device IP Address (with LibreNMS link)
- Physical Location
- Operating System
- System Version
- Hardware Information
- System Uptime

#### Conditional Content
Templates use Blade templating syntax:
- `@if($alert->state == 1)` - Active alert
- `@if($alert->state == 2)` - Acknowledged
- `@if($alert->state == 3)` - Recovering
- `@else` - Recovered/cleared

---

## Usage in LibreNMS

### Assigning Templates to Alert Rules

Templates are assigned to alert rules in the LibreNMS web interface:

1. Navigate to **Alerts** > **Alert Rules**
2. Edit or create an alert rule
3. In the **Alert Templates** section, select the appropriate template
4. Configure alert rule conditions and thresholds

See [Alert Template Assignments.png](Alert%20Template%20Assignments.png) for the current template assignments.

### Template Format

Templates are stored as Markdown files with three main sections:

1. **Alert Title**: Subject line for alert notifications
2. **Recovery Title**: Subject line for recovery notifications
3. **Alert Template**: HTML body of the email

Each section is separated by triple backticks with appropriate language hints (text or html).

### LibreNMS URL Configuration

Templates currently reference: `http://nms.cabnetworks.ca:8000`

Update this URL in templates if your LibreNMS installation uses a different address.

---

## Template Development

### Creating New Templates

1. Use `Template.md` as a starting point
2. Follow the three-section structure (Alert Title, Recovery Title, HTML Template)
3. Include standard CSS classes for consistent styling
4. Add device details section
5. Include conditional logic for different alert states
6. Add relevant recommended actions for troubleshooting
7. Test with sample alerts before deploying

### Best Practices

- **Use semantic HTML**: Tables for structured data, divs for content blocks
- **Include conditional logic**: Show different content for alerts vs. recoveries
- **Add context**: Explain what the alert means and why it matters
- **Provide actionable guidance**: Include specific troubleshooting steps
- **Use visual indicators**: Color-coded markers, progress bars, status badges
- **Test thoroughly**: Verify rendering in multiple email clients
- **Be consistent**: Follow the established color scheme and structure

### CSS Classes Reference

Common classes used across templates:

- `.box` - Main container with shadow
- `.table` - Primary data table
- `.state-marker` - Colored vertical status bar
- `.header` - Main alert header
- `.section-header` - Section dividers
- `.critical-box` - Red-bordered critical message box
- `.warning-box` - Orange-bordered warning message box
- `.recovered-box` - Green-bordered recovery message box
- `.info-box` - Blue-bordered informational box
- `.progress-bar-container` - Progress bar wrapper
- `.progress-bar` - Progress bar fill element
- `.ALERT` / `.ACKNOWLEDGED` / `.RECOVERED` - State-specific styling

---

## Maintenance

### Regular Updates

- Review and update LibreNMS URL if installation changes
- Update recommended actions based on operational experience
- Add new templates for new alert types as needed
- Keep threshold values aligned with organizational standards

### Version Control

This directory contains the production templates used by LibreNMS. Any changes should be:

1. Tested in a development environment first
2. Documented in this README
3. Reviewed for consistent formatting and styling
4. Backed up before deployment

---

## Additional Files

- **Template.md**: Blank template for creating new alert templates
- **Alert Template Assignments.png**: Visual reference showing which templates are assigned to which alert rules

---

## Contact and Support

For questions about these templates or to report issues, please contact your LibreNMS administrator.

LibreNMS Documentation: https://docs.librenms.org/

---

*Last Updated: 2025-11-14*
