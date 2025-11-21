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
    @if ($alert->state == 1)
    @if ($alert->faults)
      @php
        $affectedFilesystems = [];
        foreach ($alert->faults as $key => $value) {
          $perc = $value['storage_perc'] ?? 0;
          if ($perc >= 75) {
            $affectedFilesystems[] = $value;
          }
        }
        $maxPerc = 0;
        if (count($affectedFilesystems) > 0) {
          foreach ($affectedFilesystems as $fs) {
            $maxPerc = max($maxPerc, $fs['storage_perc'] ?? 0);
          }
        }
      @endphp
      @if(count($affectedFilesystems) > 0)
    <tr>
        <td colspan=3 class="@if($maxPerc >= 95)critical-box @elseif($maxPerc >= 90)critical-box @else warning-box @endif">
          @if($maxPerc >= 95)
            <strong>🚨 <span style="color: #bd1e1e;">SEVERE</span> Storage Utilization Detected:</strong><br>
            Filesystem usage has reached <span style="color: #bd1e1e;"><strong>severe levels (≥95%)</strong></span>. Storage capacity is critically constrained and requires immediate attention to prevent system issues.
          @elseif($maxPerc >= 90)
            <strong>🚨 <span style="color: #bd1e1e;">CRITICAL</span> Storage Utilization Detected:</strong><br>
            Filesystem usage has reached <span style="color: #bd1e1e;"><strong>critical levels (90-94%)</strong></span>. Storage capacity is severely constrained and requires prompt intervention.
          @elseif($maxPerc >= 80)
            <strong>⚠️ <span style="color: #ff9800;">WARNING</span> Storage Utilization Detected:</strong><br>
            Filesystem usage has reached <span style="color: #ff9800;"><strong>warning levels (80-89%)</strong></span>. Storage capacity should be investigated to prevent potential issues.
          @else
            <strong>⚠️ <span style="color: #ff9800;">ELEVATED</span> Storage Utilization Detected:</strong><br>
            Filesystem usage has reached <span style="color: #ff9800;"><strong>elevated levels (75-79%)</strong></span>. Storage capacity should be monitored.
          @endif
          <br><br>
          <strong>Affected Filesystems:</strong><br>
          @foreach($affectedFilesystems as $fs)
            • {{ $fs['storage_descr'] ?? 'Unknown' }} 
            (<span style="@if($fs['storage_perc'] >= 95)color: #bd1e1e;@elseif($fs['storage_perc'] >= 90)color: #bd1e1e;@elseif($fs['storage_perc'] >= 80)color: #ff9800;@else color: #ff9800;@endif">{{ $fs['storage_perc'] ?? 'N/A' }}%</span> used)<br>
          @endforeach
        </td>
    </tr>
      @else
    <tr>
        <td colspan=3 class="warning-box">
          <strong>ℹ️ Storage Alert:</strong><br>
          Filesystem usage is at <span style="color: #4caf50;"><strong>acceptable levels</strong></span>.
        </td>
    </tr>
      @endif
    @else
    <tr>
        <td colspan=3 class="warning-box">
          <strong>⚠️ High Storage Utilization Detected:</strong><br>
          Filesystem usage has exceeded acceptable levels.
        </td>
    </tr>
    @endif
    @else
    <tr>
        <td colspan=3 class="recovered-box">
          <strong>✅ Storage Alert Recovered:</strong><br>
          Filesystem utilization has returned to acceptable levels. The alert was previously triggered when storage usage exceeded the threshold.
          <br><br>
          <em>Note: This is a recovery notification confirming that storage space has been freed. Specific usage percentages and sizes shown below reflect stale data from when the alert was triggered, not current filesystem status.</em>
        </td>
    </tr>
    @endif
    @if ($alert->faults)
      @foreach ($alert->faults as $key => $value)
        @php
          $perc = $value['storage_perc'] ?? 0;
        @endphp
        @if($alert->state == 1 && $perc >= 75 || $alert->state != 1)
    <tr>
        <td colspan=2><strong>Filesystem</strong></td>
        <td><strong>{{ $value['storage_descr'] ?? 'N/A' }}</strong></td>
    </tr>
    <tr>
        <td colspan=2><strong>Mount Point</strong></td>
        <td>{{ $value['storage_mib'] ?? $value['storage_descr'] ?? 'N/A' }}</td>
    </tr>
    @if ($alert->state == 1)
    <tr>
        <td colspan=2><strong>Current Usage</strong></td>
        <td><strong style="@if($value['storage_perc'] >= 95)color: #bd1e1e;@elseif($value['storage_perc'] >= 90)color: #bd1e1e;@elseif($value['storage_perc'] >= 80)color: #ff9800;@else color: #ff9800;@endif">{{ $value['storage_perc'] ?? 'N/A' }}%</strong></td>
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
    @else
    <tr>
        <td colspan=2><strong>Usage at Alert Time</strong></td>
        <td>{{ $value['storage_perc'] ?? 'N/A' }}% <em>(stale data)</em></td>
    </tr>
    @endif
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
    <tr>
        <td colspan=3 style="padding: 10px; background-color: #f0f0f0; border-top: 2px solid #ddd;"></td>
    </tr>
        @endif
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
    <tr>
        <td colspan=2><strong>Alert Started</strong></td>
        <td>{{ $alert->timestamp }}</td>
    </tr>

    <!-- STORAGE THRESHOLDS -->
    <tr>
        <td colspan=3 class="section-header">Storage Usage Reference</td>
    </tr>
    <tr>
        <td colspan=3 style="padding: 10px;">
          <table style="width: 100%; border: none;">
            <tr>
              <td style="border: none; padding: 5px;"><span style="color: #4caf50;">●</span> <strong>Healthy:</strong> &lt; 75%</td>
              <td style="border: none; padding: 5px;"><span style="color: #ff9800;">●</span> <strong>Elevated:</strong> 75-79%</td>
            </tr>
            <tr>
              <td style="border: none; padding: 5px;"><span style="color: #ff9800;">●</span> <strong>Warning:</strong> 80-89%</td>
              <td style="border: none; padding: 5px;"><span style="color: #bd1e1e;">●</span> <strong>Critical:</strong> 90-94%</td>
            </tr>
            <tr>
              <td style="border: none; padding: 5px;"><span style="color: #bd1e1e;">●</span> <strong>Severe:</strong> ≥ 95%</td>
              <td style="border: none; padding: 5px;">&nbsp;</td>
            </tr>
          </table>
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
            <li><strong>Investigate large files and directories</strong> - Use tools like du or ncdu to identify space consumers</li>
            <li><strong>Check for old log files</strong> - Review and rotate or delete unnecessary logs</li>
            <li><strong>Review application caches</strong> - Clear temporary files and application caches</li>
            <li><strong>Verify backup processes</strong> - Ensure backups aren't filling the filesystem</li>
            <li><strong>Check for core dumps</strong> - Remove old crash dumps if present</li>
            <li><strong>Review database growth</strong> - If applicable, check database size and growth patterns</li>
            <li><strong>Plan capacity expansion</strong> - Consider expanding storage if consistently high usage</li>
            <li><strong>Implement monitoring</strong> - Set up automated cleanup policies where appropriate</li>
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
        <td>{{ $alert->name ?? 'Storage Usage Alert' }}</td>
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
```