# Port Status Up/Down (HTML)

## Alert Title
```text
@if($alert->state == 0)✅ PORT UP:@else🔴 PORT DOWN:@endif {{ strtoupper(explode('.', $alert->hostname)[0]) }} - Port Status Change
```

## Recovery Title
```text
@if($alert->state == 0)✅ PORT UP:@else🔴 PORT DOWN:@endif {{ strtoupper(explode('.', $alert->hostname)[0]) }} - Port Status Change
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

  .status-badge {
    display: inline-block;
    padding: 3px 8px;
    border-radius: 3px;
    font-weight: bold;
    font-size: 12px;
    color: white;
  }

  .status-down {
    background-color: #bd1e1e;
  }

  .status-up {
    background-color: #4caf50;
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
            <span class="ALERT">PORT DOWN</span>
          @elseif ($alert->state == 2)
            <span class="ACKNOWLEDGED">PORT STATUS - ACKNOWLEDGED</span>
          @elseif ($alert->state == 3)
            <span class="RECOVERED">PORT UP - RECOVERING</span>
          @else
            <span class="RECOVERED">PORT UP</span>
          @endif
        </td>
    </tr>

    <!-- PORT STATUS DETAILS SECTION -->
    <tr>
        <td colspan=3 class="section-header">Port Status Details</td>
    </tr>
    @if ($alert->state == 1)
    <tr>
        <td colspan=3 class="critical-box">
          <strong>🔴 <span style="color: #bd1e1e;">PORT DOWN</span> Event Detected:</strong><br>
          Network port has changed to a <span style="color: #bd1e1e;"><strong>down state</strong></span>. This may indicate a physical connectivity issue, device power loss, or intentional shutdown.
          @if ($alert->faults)
            @php
              $hasAdminDown = false;
              foreach ($alert->faults as $key => $value) {
                if (isset($value['ifAdminStatus']) && $value['ifAdminStatus'] == 'down') {
                  $hasAdminDown = true;
                  break;
                }
              }
            @endphp
            @if($hasAdminDown)
              <br><br><em>Note: Administrative status is DOWN - port may be intentionally disabled.</em>
            @endif
          @endif
        </td>
    </tr>
    @else
    <tr>
        <td colspan=3 class="recovered-box">
          <strong>✅ <span style="color: #4caf50;">PORT UP</span> Event Detected:</strong><br>
          Network port has changed to an <span style="color: #4caf50;"><strong>up state</strong></span>. Port connectivity has been restored and is now operational.
        </td>
    </tr>
    @endif
    @if ($alert->faults)
      @php
        // Filter to only show the port(s) that actually triggered the alert
        // This should typically be only 1 port for port status alerts
        $affectedPorts = [];
        foreach ($alert->faults as $key => $value) {
          // Only include ports where status actually changed or is in an alert state
          if (isset($value['ifOperStatus']) && isset($value['ifOperStatus_prev'])) {
            if ($value['ifOperStatus'] != $value['ifOperStatus_prev']) {
              $affectedPorts[] = $value;
            }
          } else {
            // If we don't have previous status, include the first port only
            if (empty($affectedPorts)) {
              $affectedPorts[] = $value;
            }
          }
        }
        // Fallback: if no ports detected as changed, just take the first one
        if (empty($affectedPorts) && count($alert->faults) > 0) {
          $affectedPorts[] = reset($alert->faults);
        }
      @endphp
      @foreach ($affectedPorts as $value)
    <tr>
        <td colspan=2><strong>Port Interface</strong></td>
        <td><strong>{{ $value['ifDescr'] ?? $value['ifName'] ?? 'N/A' }}</strong></td>
    </tr>
    <tr>
        <td colspan=2><strong>Port Description</strong></td>
        <td>{{ $value['ifAlias'] ?? 'No description set' }}</td>
    </tr>
    <tr>
        <td colspan=2><strong>Operational Status</strong></td>
        <td>
          @if(isset($value['ifOperStatus']))
            @if($value['ifOperStatus'] == 'up')
              <span class="status-badge status-up">UP</span>
            @else
              <span class="status-badge status-down">DOWN</span>
            @endif
            ({{ $value['ifOperStatus'] }})
          @else
            N/A
          @endif
        </td>
    </tr>
    <tr>
        <td colspan=2><strong>Administrative Status</strong></td>
        <td>
          @if(isset($value['ifAdminStatus']))
            @if($value['ifAdminStatus'] == 'up')
              <span class="status-badge status-up">UP</span>
            @else
              <span class="status-badge status-down">DOWN</span>
            @endif
            ({{ $value['ifAdminStatus'] }})
          @else
            N/A
          @endif
        </td>
    </tr>
    <tr>
        <td colspan=2><strong>Port Speed</strong></td>
        <td>
          @if(isset($value['ifSpeed']))
            @php
              $speed = $value['ifSpeed'];
              if ($speed >= 1000000000) {
                echo number_format($speed / 1000000000, 0) . ' Gbps';
              } elseif ($speed >= 1000000) {
                echo number_format($speed / 1000000, 0) . ' Mbps';
              } else {
                echo number_format($speed / 1000, 0) . ' Kbps';
              }
            @endphp
          @else
            N/A
          @endif
        </td>
    </tr>
    <tr>
        <td colspan=2><strong>Port Type</strong></td>
        <td>{{ $value['ifType'] ?? 'N/A' }}</td>
    </tr>
    <tr>
        <td colspan=2><strong>MAC Address</strong></td>
        <td>{{ $value['ifPhysAddress'] ?? 'N/A' }}</td>
    </tr>
    @if(isset($value['ifLastChange']))
    <tr>
        <td colspan=2><strong>Last Status Change</strong></td>
        <td>{{ $value['ifLastChange'] }}</td>
    </tr>
    @endif
      @endforeach
    @endif
    <tr>
        <td colspan=2><strong>Event Duration</strong></td>
        <td>{{ $alert->elapsed }}</td>
    </tr>
    <tr>
        <td colspan=2><strong>Event Time</strong></td>
        <td>{{ $alert->timestamp }}</td>
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
    <tr>
        <td colspan=2><strong>System Uptime</strong></td>
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
            @if ($alert->faults)
              @foreach ($alert->faults as $key => $value)
                @if(isset($value['ifAdminStatus']) && $value['ifAdminStatus'] == 'down')
                  <li><strong>Check administrative status</strong> - Port is administratively disabled, may need to be manually enabled</li>
                @else
                  <li><strong>Verify physical connectivity</strong> - Check cable connections at both ends</li>
                  <li><strong>Check cable integrity</strong> - Test or replace network cable if damaged</li>
                  <li><strong>Verify connected device</strong> - Ensure the device on the other end is powered on</li>
                  <li><strong>Check for hardware issues</strong> - Verify port is not damaged or disabled</li>
                  <li><strong>Review switch logs</strong> - Check for error messages or port security violations</li>
                  <li><strong>Test port functionality</strong> - Try connecting a known-good device to the port</li>
                  <li><strong>Check auto-negotiation</strong> - Verify speed/duplex settings are correct</li>
                @endif
              @endforeach
            @else
              <li><strong>Verify physical connectivity</strong> - Check cable connections at both ends</li>
              <li><strong>Check connected device</strong> - Ensure the device on the other end is powered on</li>
              <li><strong>Review switch logs</strong> - Check for error messages or port security violations</li>
            @endif
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
        <td>{{ $alert->name ?? 'Port Status Change' }}</td>
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
```