# Device Down - SNMP Unreachable (HTML)

## Alert Title
```text
@if($alert->state == 0)✅ DEVICE UP:@else🚨 DEVICE DOWN:@endif {{ strtoupper(explode('.', $alert->hostname)[0]) }} - SNMP Unreachable
```

## Recovery Title
```text
@if($alert->state == 0)✅ DEVICE UP:@else🚨 DEVICE DOWN:@endif {{ strtoupper(explode('.', $alert->hostname)[0]) }} - SNMP Unreachable
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
    background-color: #bd1e1e;
  }

  .table tbody > tr.ALERT > td {
    background-color: #ffebee;
  }

  .ALERT {
    color: red;
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

  .critical-box {
    background-color: #ffebee;
    border-left: 4px solid #bd1e1e;
    padding: 10px;
    margin: 10px 0;
  }

  .recovered-box {
    background-color: #ebffec;
    border-left: 4px solid #4caf50;
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
            <span class="ALERT">DEVICE DOWN - SNMP UNREACHABLE</span>
          @elseif ($alert->state == 2)
            <span class="ACKNOWLEDGED">DEVICE DOWN - ACKNOWLEDGED</span>
          @elseif ($alert->state == 3)
            <span class="RECOVERED">DEVICE UP - RECOVERING</span>
          @else
            <span class="RECOVERED">DEVICE UP - RECOVERED</span>
          @endif
        </td>
    </tr>

    <!-- OUTAGE DETAILS SECTION -->
    <tr>
        <td colspan=3 class="section-header">Outage Details</td>
    </tr>
    @if ($alert->state == 1)
    <tr>
        <td colspan=3 class="critical-box">
          <strong>⚠️ SNMP Monitoring Lost:</strong> Unable to communicate with device via SNMP. The device may be down, SNMP service may have stopped, or network connectivity may be interrupted.
        </td>
    </tr>
    @else
    <tr>
        <td colspan=3 class="recovered-box">
          <strong>✅ SNMP Communication Restored:</strong> Device is now responding to SNMP queries.
        </td>
    </tr>
    @endif
    <tr>
        <td colspan=2><strong>Outage Duration</strong></td>
        <td><strong style="color: #bd1e1e;">{{ $alert->elapsed }}</strong></td>
    </tr>
    <tr>
        <td colspan=2><strong>Alert Started</strong></td>
        <td>{{ $alert->timestamp }}</td>
    </tr>

    <!-- DEVICE DETAILS SECTION -->
    <tr>
        <td colspan=3 class="section-header">Device Details</td>
    </tr>
    <tr>
        <td colspan=2><strong>Hostname (System Name)</strong></td>
        <td><strong><a href="http://nms.cabnetworks.ca:8000/device/{{ $alert->device_id }}/" class="entity-popup red" data-eid="{{ $alert->device_id }}" data-etype="device">{{ $alert->hostname }} ({{ $alert->sysName }})</a></strong></td>
    </tr>
    <tr>
        <td colspan=2><strong>Device IP</strong></td>
        <td><strong><a href="http://nms.cabnetworks.ca:8000/device/{{ $alert->device_id }}/" class="entity-popup red" data-eid="{{ $alert->device_id }}" data-etype="device">{{ $alert->ip }}</a></strong></td>
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
    <tr>
        <td colspan=2><strong>Last Known Uptime</strong></td>
        <td>{{ $alert->uptime_long }}</td>
    </tr>

    <!-- ACTION ITEMS SECTION -->
    @if ($alert->state == 1)
    <tr>
        <td colspan=3 class="section-header">Recommended Actions</td>
    </tr>
    <tr>
        <td colspan=3 style="padding: 10px;">
          <ul style="margin: 5px 0; padding-left: 20px;">
            <li><strong>Verify device power status</strong> - Check if device is powered on</li>
            <li><strong>Check network connectivity</strong> - Verify device is reachable on the network</li>
            <li><strong>Test SNMP service</strong> - Confirm SNMP daemon is running on the device</li>
            <li><strong>Verify SNMP credentials</strong> - Ensure community string or SNMPv3 credentials are correct</li>
            <li><strong>Check firewall rules</strong> - Verify SNMP traffic (UDP 161) is not being blocked</li>
            <li><strong>Review device logs</strong> - Check for recent crashes or service failures</li>
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
        <td>{{ $alert->name ?? 'Device Down - SNMP Unreachable' }}</td>
    </tr>
    <tr>
        <td colspan=2><strong>Severity</strong></td>
        <td><strong style="color: #bd1e1e;">{{ $alert->severity }}</strong></td>
    </tr>
    <tr>
        <td colspan=2><strong>Unique ID</strong></td>
        <td>{{ $alert->uid }}</td>
    </tr>
  </tbody>
</table>
</body>
</html>
``