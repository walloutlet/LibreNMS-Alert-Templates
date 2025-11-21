# Device Rebooted (HTML)

## Alert Title
```text
@if($alert->state == 0)✅ CLEARED:@else🔄 REBOOTED:@endif {{ strtoupper(explode('.', $alert->hostname)[0]) }} - Uptime: {{ $alert->uptime_short }}
```

## Recovery Title
```text
@if($alert->state == 0)✅ CLEARED:@else🔄 REBOOTED:@endif {{ strtoupper(explode('.', $alert->hostname)[0]) }} - Uptime: {{ $alert->uptime_short }}
```

---

## Alert Template
```html
<html>
<head>
  <title>{{ $alert->title }}</title>
  <style type="text/css">

  body {
    margin: 15px;
    font-family: 'Source Sans Pro', 'Helvetica Neue', Helvetica, Arial, sans-serif;
    font-size: 14px;
    line-height: 20px;
    color: #333333;
    background-color: #fff;
    font-weight: 400;
  }

  .box {
    position: relative;
    -webkit-border-radius: 0px;
    -moz-border-radius: 0px;
    border-radius: 0px;
    background: #ffffff;
    margin-bottom: 10px;
    width: 100%;
    box-shadow: 0 0 2px rgba(0, 0, 0, 0.12), 0 2px 4px rgba(0, 0, 0, 0.24);
  }

  .no-padding {
    padding: 0 !important;
  }

  .table {
    max-width: 800px;
    background-color: #fff;
    border-collapse: collapse;
    border-spacing: 0;
    color: #333;
  }

  .table th, .table td {
    padding: 5px 10px;
    line-height: 20px;
    text-align: left;
    vertical-align: top;
    border-top: 1px solid #f4f4f4;
  }

  .table tbody > tr:nth-child(even) > td, .table tbody > tr:nth-child(even) > th {
    background-color: #f9f9f9;
  }

  .table .state-marker, .state-marker {
    padding: 0px;
    margin: none;
    width: 7px;
  }

  .table tbody > tr > td.state-marker {
    background-color: #004774;
  }

  .table .ALERT .state-marker {
    background-color: #ff9800;
  }

  .table tbody > tr.ALERT > td {
    background-color: #fff8eb;
  }

  .ALERT {
    color: #ff9800;
  }

  .table .ACKNOWLEDGED .state-marker {
    background-color: rgb(187, 135, 4);
  }

  .table tbody > tr.ACKNOWLEDGED > td {
    background-color: #fff8eb;
  }

  .ACKNOWLEDGED {
    color: goldenrod;
  }

  .table .RECOVERED .state-marker {
    background-color: rgb(0, 92, 0);
  }

  .table tbody > tr.RECOVERED > td {
    background-color: #ebffec;
  }

  .RECOVERED {
    color: green;
  }

  .header { 
    font-size: 20px; 
    font-weight: bold; 
    padding: 10px;
  }

  .section-header {
    font-size: 16px;
    font-weight: bold;
    padding: 10px;
    text-transform: uppercase;
    text-align: center;
  }

  .warning-box {
    background-color: #fff3cd;
    border-left: 4px solid #ff9800;
    padding: 10px;
    margin: 10px 0;
  }

  .info-box {
    background-color: #e3f2fd;
    border-left: 4px solid #2196f3;
    padding: 10px;
    margin: 10px 0;
  }

  </style>
</head>
<body>
<table class="table box box-solid">
  <tbody>
    @if ($alert->state == 1)
      <tr class="ALERT">
    @elseif ($alert->state == 2)
      <tr class="ACKNOWLEDGED">
    @elseif ($alert->state == 3)
      <tr class="RECOVERED">
    @else
      <tr class="RECOVERED">
    @endif
        <td class="state-marker"></td>
        <td colspan=2 style="padding: 10px; text-transform: uppercase;" class="header">
          @if ($alert->state == 1)
            <span class="ALERT">DEVICE REBOOTED</span>
          @elseif ($alert->state == 2)
            <span class="ACKNOWLEDGED">REBOOT - ACKNOWLEDGED</span>
          @elseif ($alert->state == 3)
            <span class="RECOVERED">REBOOT - RECOVERING</span>
          @else
            <span class="RECOVERED">REBOOT - CLEARED</span>
          @endif
        </td>
    </tr>

    <!-- REBOOT DETAILS SECTION -->
    <tr>
        <td colspan=3 class="section-header">Reboot Event Details</td>
    </tr>
    @if ($alert->state == 1)
    <tr>
        <td colspan=3 class="warning-box">
          <strong>🔄 Device Reboot Detected:</strong> The device uptime has reset, indicating a reboot or power cycle occurred. This may have been planned maintenance or an unexpected restart.
        </td>
    </tr>
    @else
    <tr>
        <td colspan=3 class="info-box">
          <strong>ℹ️ Reboot Event Cleared:</strong> This notification has been acknowledged or the monitoring window has passed.
        </td>
    </tr>
    @endif
    <tr>
        <td colspan=2><strong>Current Uptime</strong></td>
        <td><strong>{{ $alert->uptime_long }}</strong></td>
    </tr>
    <tr>
        <td colspan=2><strong>Reboot Detected At</strong></td>
        <td>{{ $alert->timestamp }}</td>
    </tr>
    <tr>
        <td colspan=2><strong>Time Since Detection</strong></td>
        <td>{{ $alert->elapsed }}</td>
    </tr>

    <!-- DEVICE DETAILS SECTION -->
    <tr>
        <td colspan=3 class="section-header">Device Details</td>
    </tr>
    <tr>
        <td colspan=2><strong>Hostname (System Name)</strong></td>
        <td><strong><a href="http://your.librenms.url/device/{{ $alert->device_id }}/" class="entity-popup red" data-eid="{{ $alert->device_id }}" data-etype="device">{{ $alert->hostname }} ({{ $alert->sysName }})</a></strong></td>
    </tr>
    <tr>
        <td colspan=2><strong>Device IP</strong></td>
        <td><strong><a href="http://your.librenms.url/device/{{ $alert->device_id }}/" class="entity-popup red" data-eid="{{ $alert->device_id }}" data-etype="device">{{ $alert->ip }}</a></strong></td>
    </tr>
    <tr>
        <td colspan=2><strong>Device Location</strong></td>
        <td>{{ $alert->location }}</td>
    </tr>
    <tr>
        <td colspan=2><strong>Operating System</strong></td>
        <td>{{ $alert->os }}</td>
    </tr>
    <tr>
        <td colspan=2><strong>System Version</strong></td>
        <td>{{ $alert->version }}</td>
    </tr>
    <tr>
        <td colspan=2><strong>Hardware</strong></td>
        <td>{{ $alert->hardware }}</td>
    </tr>

    <!-- ACTION ITEMS SECTION -->
    @if ($alert->state == 1)
    <tr>
        <td colspan=3 class="section-header">Recommended Actions</td>
    </tr>
    <tr>
        <td colspan=3 style="padding: 10px;">
          <ul style="margin: 5px 0; padding-left: 20px;">
            <li><strong>Verify planned maintenance</strong> - Check if this was a scheduled reboot or maintenance window</li>
            <li><strong>Review system logs</strong> - Check device logs for reason of reboot (crash, update, manual restart)</li>
            <li><strong>Check for updates</strong> - Verify if automatic updates triggered the reboot</li>
            <li><strong>Monitor stability</strong> - Watch for additional unexpected reboots that may indicate hardware issues</li>
            <li><strong>Verify services</strong> - Confirm all critical services have restarted properly</li>
            <li><strong>Check for power issues</strong> - If unplanned, investigate potential power supply problems</li>
            <li><strong>Document the event</strong> - Record reboot reason and any related issues for future reference</li>
          </ul>
        </td>
    </tr>
    @endif

    <!-- ALERT METADATA -->
    <tr>
        <td colspan=3 class="section-header">Alert Information</td>
    </tr>
    <tr>
        <td colspan=2><strong>Alert Rule</strong></td>
        <td>{{ $alert->name ?? 'Device Rebooted' }}</td>
    </tr>
    <tr>
        <td colspan=2><strong>Severity</strong></td>
        <td><strong style="color: #ff9800;">{{ $alert->severity }}</strong></td>
    </tr>
    <tr>
        <td colspan=2><strong>Unique ID</strong></td>
        <td>{{ $alert->uid }}</td>
    </tr>
  </tbody>
</table>
</body>
</html>
```