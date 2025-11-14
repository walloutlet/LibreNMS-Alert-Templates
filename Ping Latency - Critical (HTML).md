# Ping Latency - Critical (HTML)

## Alert Title
```text
@if($alert->state == 0)✅ RECOVERED:@else🚨 CRITICAL:@endif {{ strtoupper(explode('.', $alert->hostname)[0]) }} - High Ping Latency
```

## Recovery Title
```text
@if($alert->state == 0)✅ RECOVERED:@else🚨 CRITICAL:@endif {{ strtoupper(explode('.', $alert->hostname)[0]) }} - High Ping Latency
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

  .recovered-box {
    background-color: #ebffec;
    border-left: 4px solid #4caf50;
    padding: 10px;
    margin: 10px 0;
  }

  .progress-bar-container {
    background-color: #f0f0f0;
    border-radius: 4px;
    height: 20px;
    width: 100%;
    overflow: hidden;
    max-width: 400px;
  }

  .progress-bar {
    height: 100%;
    text-align: center;
    line-height: 20px;
    color: white;
    font-weight: bold;
    font-size: 12px;
  }

  .progress-excellent {
    background-color: #2196f3;
  }

  .progress-healthy {
    background-color: #4caf50;
  }

  .progress-warning {
    background-color: #ff9800;
  }

  .progress-critical {
    background-color: #bd1e1e;
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
            <span class="ALERT">HIGH PING LATENCY</span>
          @elseif ($alert->state == 2)
            <span class="ACKNOWLEDGED">LATENCY - ACKNOWLEDGED</span>
          @elseif ($alert->state == 3)
            <span class="RECOVERED">LATENCY - RECOVERING</span>
          @else
            <span class="RECOVERED">LATENCY - NORMAL</span>
          @endif
        </td>
    </tr>

    <!-- LATENCY DETAILS SECTION -->
    <tr>
        <td colspan=3 class="section-header">Latency Details</td>
    </tr>
    @if ($alert->state == 1)
    <tr>
        <td colspan=3 class="warning-box">
          <strong>⚠️ High Network Latency Detected:</strong> Ping response times are elevated, which may indicate network congestion, routing issues, or device performance problems affecting user experience.
        </td>
    </tr>
    @else
    <tr>
        <td colspan=3 class="recovered-box">
          <strong>✅ Latency Returned to Normal:</strong> Ping response times have returned to acceptable levels.
        </td>
    </tr>
    @endif
    @if ($alert->faults)
      @foreach ($alert->faults as $key => $value)
    <tr>
        <td colspan=2><strong>Current Latency</strong></td>
        <td>
          <strong style="@if($value['device_perf_avg'] >= 10)color: #bd1e1e;@elseif($value['device_perf_avg'] >= 5)color: #ff9800;@elseif($value['device_perf_avg'] >= 1)color: #4caf50;@else color: #2196f3;@endif">
            {{ $value['device_perf_avg'] ?? $value['last_ping_timetaken'] ?? 'N/A' }} ms
          </strong>
        </td>
    </tr>
    <tr>
        <td colspan=3 style="padding: 10px;">
          <div class="progress-bar-container">
            @php
              $latency = $value['device_perf_avg'] ?? $value['last_ping_timetaken'] ?? 0;
              $percent = min(($latency / 20) * 100, 100); // Scale to 20ms max for visualization
            @endphp
            <div class="progress-bar @if($latency >= 10)progress-critical @elseif($latency >= 5)progress-warning @elseif($latency >= 1)progress-healthy @else progress-excellent @endif" 
                 style="width: {{ max($percent, 5) }}%;">
              {{ $latency }} ms
            </div>
          </div>
        </td>
    </tr>
      @endforeach
    @endif
    <tr>
        <td colspan=2><strong>Alert Duration</strong></td>
        <td>{{ $alert->elapsed }}</td>
    </tr>
    <tr>
        <td colspan=2><strong>Alert Started</strong></td>
        <td>{{ $alert->timestamp }}</td>
    </tr>

    <!-- LATENCY THRESHOLDS -->
    <tr>
        <td colspan=3 class="section-header">Latency Reference</td>
    </tr>
    <tr>
        <td colspan=3 style="padding: 10px;">
          <table style="width: 100%; border: none;">
            <tr>
              <td style="border: none; padding: 5px;"><span style="color: #2196f3;">●</span> <strong>Excellent:</strong> &lt; 1ms</td>
              <td style="border: none; padding: 5px;"><span style="color: #4caf50;">●</span> <strong>Good:</strong> 1-5ms</td>
            </tr>
            <tr>
              <td style="border: none; padding: 5px;"><span style="color: #ff9800;">●</span> <strong>Warning:</strong> 5-10ms</td>
              <td style="border: none; padding: 5px;"><span style="color: #bd1e1e;">●</span> <strong>Critical:</strong> &gt; 10ms</td>
            </tr>
          </table>
          <p style="margin-top: 10px; font-size: 12px; color: #666;">
            <em>Note: This alert monitors ping response time (latency). Packet loss monitoring requires separate alert rules and may not be available in all configurations.</em>
          </p>
        </td>
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
            <li><strong>Check network path</strong> - Use traceroute to identify where latency is occurring</li>
            <li><strong>Monitor bandwidth usage</strong> - Check if network links are saturated</li>
            <li><strong>Review device CPU/memory</strong> - High device load can cause delayed responses</li>
            <li><strong>Check for routing issues</strong> - Verify optimal routing paths are being used</li>
            <li><strong>Test from multiple sources</strong> - Determine if issue is localized or widespread</li>
            <li><strong>Review QoS settings</strong> - Ensure proper Quality of Service configuration</li>
            <li><strong>Check ISP status</strong> - For internet-facing devices, verify provider performance</li>
            <li><strong>Monitor for patterns</strong> - Check if latency spikes occur at specific times</li>
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
        <td>{{ $alert->name ?? 'High Ping Latency' }}</td>
    </tr>
    <tr>
        <td colspan=2><strong>Severity</strong></td>
        <td><strong style="@if($alert->severity == 'critical')color: #bd1e1e;@else color: #ff9800;@endif">{{ $alert->severity }}</strong></td>
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