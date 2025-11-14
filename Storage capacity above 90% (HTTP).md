# Storage capacity above 90% (HTTP)

## Alert Title
```text
@if($alert->state == 0)✅ RECOVERED:@else🚨 CRITICAL:@endif {{ strtoupper(explode('.', $alert->hostname)[0]) }} - @if($alert->faults)@foreach($alert->faults as $key => $value){{ $value['storage_descr'] }} at {{ $value['storage_perc'] }}%@endforeach @endif c
```

## Recovery Title
```text
@if($alert->state == 0)✅ RECOVERED:@else🚨 CRITICAL:@endif {{ strtoupper(explode('.', $alert->hostname)[0]) }} - @if($alert->faults)@foreach($alert->faults as $key => $value){{ $value['storage_descr'] }} at {{ $value['storage_perc'] }}%@endforeach @endif c
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

  .warning-box {
    background-color: #fff3cd;
    border-left: 4px solid #ff9800;
    padding: 10px;
    margin: 10px 0;
  }

  .critical-box {
    background-color: #ffebee;
    border-left: 4px solid #bd1e1e;
    padding: 10px;
    margin: 10px 0;
  }

  .progress-bar-container {
    background-color: #f0f0f0;
    border-radius: 4px;
    height: 20px;
    width: 100%;
    overflow: hidden;
  }

  .progress-bar {
    height: 100%;
    text-align: center;
    line-height: 20px;
    color: white;
    font-weight: bold;
    font-size: 12px;
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
            <span class="ALERT">STORAGE ALERT - {{ $alert->severity }}</span>
          @elseif ($alert->state == 2)
            <span class="ACKNOWLEDGED">STORAGE ALERT - ACKNOWLEDGED</span>
          @elseif ($alert->state == 3)
            <span class="RECOVERED">STORAGE ALERT - RECOVERING</span>
          @else
            <span class="RECOVERED">STORAGE ALERT - RECOVERED</span>
          @endif
        </td>
    </tr>

    <!-- STORAGE DETAILS SECTION -->
    <tr>
        <td colspan=3 class="section-header">Storage Details</td>
    </tr>
    @if ($alert->faults)
      @foreach ($alert->faults as $key => $value)
    <tr>
        <td colspan=2><strong>Filesystem</strong></td>
        <td><strong>{{ $value['storage_descr'] ?? 'N/A' }}</strong></td>
    </tr>
    <tr>
        <td colspan=2><strong>Mount Point</strong></td>
        <td>{{ $value['storage_mib'] ?? $value['storage_descr'] ?? 'N/A' }}</td>
    </tr>
    <tr>
        <td colspan=2><strong>Current Usage</strong></td>
        <td><strong style="@if($value['storage_perc'] >= 90)color: #bd1e1e;@elseif($value['storage_perc'] >= 75)color: #ff9800;@else color: #4caf50;@endif">{{ $value['storage_perc'] ?? 'N/A' }}%</strong></td>
    </tr>
    <tr>
        <td colspan=3 style="padding: 10px;">
          <div class="progress-bar-container">
            <div class="progress-bar @if($value['storage_perc'] >= 90)progress-critical @elseif($value['storage_perc'] >= 75)progress-warning @else progress-healthy @endif" 
                 style="width: {{ $value['storage_perc'] ?? 0 }}%;">
              {{ $value['storage_perc'] ?? 0 }}%
            </div>
          </div>
        </td>
    </tr>
    <tr>
        <td colspan=2><strong>Total Size</strong></td>
        <td>
          @php
            $size = $value['storage_size'] ?? 0;
            if ($size >= 1099511627776) {
              echo number_format($size / 1099511627776, 2) . ' TB';
            } elseif ($size >= 1073741824) {
              echo number_format($size / 1073741824, 2) . ' GB';
            } elseif ($size >= 1048576) {
              echo number_format($size / 1048576, 2) . ' MB';
            } elseif ($size >= 1024) {
              echo number_format($size / 1024, 2) . ' KB';
            } else {
              echo $size . ' bytes';
            }
          @endphp
        </td>
    </tr>
    <tr>
        <td colspan=2><strong>Used Space</strong></td>
        <td>
          @php
            $used = $value['storage_used'] ?? 0;
            if ($used >= 1099511627776) {
              echo number_format($used / 1099511627776, 2) . ' TB';
            } elseif ($used >= 1073741824) {
              echo number_format($used / 1073741824, 2) . ' GB';
            } elseif ($used >= 1048576) {
              echo number_format($used / 1048576, 2) . ' MB';
            } elseif ($used >= 1024) {
              echo number_format($used / 1024, 2) . ' KB';
            } else {
              echo $used . ' bytes';
            }
          @endphp
        </td>
    </tr>
    <tr>
        <td colspan=2><strong>Free Space</strong></td>
        <td>
          @php
            $free = $value['storage_free'] ?? 0;
            if ($free >= 1099511627776) {
              echo number_format($free / 1099511627776, 2) . ' TB';
            } elseif ($free >= 1073741824) {
              echo number_format($free / 1073741824, 2) . ' GB';
            } elseif ($free >= 1048576) {
              echo number_format($free / 1048576, 2) . ' MB';
            } elseif ($free >= 1024) {
              echo number_format($free / 1024, 2) . ' KB';
            } else {
              echo $free . ' bytes';
            }
          @endphp
        </td>
    </tr>
      @endforeach
    @else
    <tr>
        <td colspan=3>No storage fault details available</td>
    </tr>
    @endif
    <tr>
        <td colspan=2><strong>Alert Duration</strong></td>
        <td>{{ $alert->elapsed }}</td>
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
            <li>Investigate large files and directories on the filesystem</li>
            <li>Check for old log files that can be rotated or deleted</li>
            <li>Review application caches and temporary files</li>
            <li>Consider expanding storage if consistently high usage</li>
            <li>Verify backup and archiving processes are working correctly</li>
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
        <td>{{ $alert->name ?? 'Root Filesystem Usage >= 90%' }}</td>
    </tr>
    <tr>
        <td colspan=2><strong>Severity</strong></td>
        <td><strong style="@if($alert->severity == 'critical')color: #bd1e1e;@endif">{{ $alert->severity }}</strong></td>
    </tr>
    <tr>
        <td colspan=2><strong>Timestamp</strong></td>
        <td>{{ $alert->timestamp }}</td>
    </tr>
  </tbody>
</table>
</body>
</html>
``