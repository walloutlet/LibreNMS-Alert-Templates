# Port Utilization - Critical (HTML)

## Alert Title
```text
@if($alert->state == 0)✅ NORMAL:@else🚨 CRITICAL UTILIZATION:@endif {{ strtoupper(explode('.', $alert->hostname)[0]) }} - Critical Port Usage
```

## Recovery Title
```text
@if($alert->state == 0)✅ NORMAL:@else🚨 CRITICAL UTILIZATION:@endif {{ strtoupper(explode('.', $alert->hostname)[0]) }} - Critical Port Usage
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

  .progress-normal {
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
            <span class="ALERT">HIGH PORT UTILIZATION</span>
          @elseif ($alert->state == 2)
            <span class="ACKNOWLEDGED">PORT UTILIZATION - ACKNOWLEDGED</span>
          @elseif ($alert->state == 3)
            <span class="RECOVERED">PORT UTILIZATION - RECOVERING</span>
          @else
            <span class="RECOVERED">PORT UTILIZATION - NORMAL</span>
          @endif
        </td>
    </tr>

    <!-- UTILIZATION DETAILS SECTION -->
    <tr>
        <td colspan=3 class="section-header">Port Utilization Details</td>
    </tr>
    @if ($alert->state == 1)
    @if ($alert->faults)
      @php
        $offendingPorts = [];
        foreach ($alert->faults as $key => $value) {
          $inUtil = $value['ifInOctets_perc'] ?? 0;
          $outUtil = $value['ifOutOctets_perc'] ?? 0;
          $maxUtil = max($inUtil, $outUtil);
          if ($maxUtil >= 80) {
            $offendingPorts[] = $value;
          }
        }
      @endphp
      @if(count($offendingPorts) > 0)
        @php
          $firstPort = $offendingPorts[0];
          $utilPerc = max($firstPort['ifInOctets_perc'] ?? 0, $firstPort['ifOutOctets_perc'] ?? 0);
        @endphp
    <tr>
        <td colspan=3 class="@if($utilPerc >= 95)critical-box @else warning-box @endif">
          @if($utilPerc >= 95)
            <strong>🚨 <span style="color: #bd1e1e;">CRITICAL</span> Port Utilization Detected:</strong><br>
            Port bandwidth utilization has reached <span style="color: #bd1e1e;"><strong>critical levels (&gt;95%)</strong></span>. Network capacity is severely constrained and requires immediate intervention to prevent service degradation.
          @elseif($utilPerc >= 80)
            <strong>⚠️ <span style="color: #ff9800;">WARNING</span> Port Utilization Detected:</strong><br>
            Port bandwidth utilization has reached <span style="color: #ff9800;"><strong>warning levels (80-95%)</strong></span>. Network capacity is constrained and should be investigated to prevent potential bottlenecks.
          @endif
          <br><br>
          <strong>Affected Ports:</strong><br>
          @foreach($offendingPorts as $port)
            • {{ $port['ifDescr'] ?? $port['ifName'] ?? 'Unknown' }} 
            (@if(isset($port['ifInOctets_perc']) && isset($port['ifOutOctets_perc']))
              In: {{ number_format($port['ifInOctets_perc'], 1) }}%, Out: {{ number_format($port['ifOutOctets_perc'], 1) }}%
            @elseif(isset($port['ifInOctets_perc']))
              In: {{ number_format($port['ifInOctets_perc'], 1) }}%
            @elseif(isset($port['ifOutOctets_perc']))
              Out: {{ number_format($port['ifOutOctets_perc'], 1) }}%
            @endif)<br>
          @endforeach
        </td>
    </tr>
      @else
    <tr>
        <td colspan=3 class="warning-box">
          <strong>ℹ️ Port Utilization Alert:</strong><br>
          Port bandwidth utilization is at <span style="color: #4caf50;"><strong>normal levels (&lt;79%)</strong></span>.
        </td>
    </tr>
      @endif
    @else
    <tr>
        <td colspan=3 class="warning-box">
          <strong>⚠️ High Port Utilization Detected:</strong><br>
          Port bandwidth utilization has exceeded acceptable levels.
        </td>
    </tr>
    @endif
    @else
    @if ($alert->faults)
      @php
        $offendingPorts = [];
        foreach ($alert->faults as $key => $value) {
          $inUtil = $value['ifInOctets_perc'] ?? 0;
          $outUtil = $value['ifOutOctets_perc'] ?? 0;
          $maxUtil = max($inUtil, $outUtil);
          if ($maxUtil >= 80) {
            $offendingPorts[] = ['port' => $value, 'util' => $maxUtil, 'status' => 'warning'];
          } elseif ($maxUtil >= 70) {
            // Include ports that are close to threshold for context
            $offendingPorts[] = ['port' => $value, 'util' => $maxUtil, 'status' => 'recovered'];
          }
        }
      @endphp
      @if(count($offendingPorts) > 0)
        @php
          $firstPort = $offendingPorts[0];
          $utilPerc = $firstPort['util'];
        @endphp
    <tr>
        <td colspan=3 class="recovered-box">
          @if($utilPerc < 80)
            <strong>✅ Utilization Returned to <span style="color: #4caf50;">Normal</span> Levels:</strong><br>
            Port bandwidth utilization is now at <span style="color: #4caf50;"><strong>normal levels (&lt;79%)</strong></span>.
          @elseif($utilPerc < 95)
            <strong>✅ Utilization at <span style="color: #ff9800;">Warning</span> Levels:</strong><br>
            Port bandwidth utilization is currently at <span style="color: #ff9800;"><strong>warning levels (80-95%)</strong></span>. Continue monitoring.
          @else
            <strong>✅ Alert Cleared:</strong><br>
            Port bandwidth utilization is at <span style="color: #bd1e1e;"><strong>critical levels (&gt;95%)</strong></span>. Issue may still require attention.
          @endif
          <br><br>
          <strong>Port Status:</strong><br>
          @foreach($offendingPorts as $portData)
            @php
              $port = $portData['port'];
              $inUtil = $port['ifInOctets_perc'] ?? 0;
              $outUtil = $port['ifOutOctets_perc'] ?? 0;
            @endphp
            • {{ $port['ifDescr'] ?? $port['ifName'] ?? 'Unknown' }} 
            (@if(isset($port['ifInOctets_perc']) && isset($port['ifOutOctets_perc']))
              In: {{ number_format($inUtil, 1) }}%, Out: {{ number_format($outUtil, 1) }}%
            @elseif(isset($port['ifInOctets_perc']))
              In: {{ number_format($inUtil, 1) }}%
            @elseif(isset($port['ifOutOctets_perc']))
              Out: {{ number_format($outUtil, 1) }}%
            @endif)<br>
          @endforeach
        </td>
    </tr>
      @else
    <tr>
        <td colspan=3 class="recovered-box">
          <strong>✅ Utilization Returned to Normal:</strong><br>
          Port bandwidth utilization has returned to normal.
        </td>
    </tr>
      @endif
    @else
    <tr>
        <td colspan=3 class="recovered-box">
          <strong>✅ Utilization Returned to Normal:</strong><br>
          Port bandwidth utilization has returned to normal.
        </td>
    </tr>
    @endif
    @endif
    @if ($alert->faults)
      @foreach ($alert->faults as $key => $value)
        @php
          $inUtil = $value['ifInOctets_perc'] ?? 0;
          $outUtil = $value['ifOutOctets_perc'] ?? 0;
          $maxUtil = max($inUtil, $outUtil);
        @endphp
        @if($maxUtil >= 80)
    <tr>
        <td colspan=2><strong>Port Interface</strong></td>
        <td><strong>{{ $value['ifDescr'] ?? $value['ifName'] ?? 'N/A' }}</strong></td>
    </tr>
    <tr>
        <td colspan=2><strong>Port Description</strong></td>
        <td>{{ $value['ifAlias'] ?? 'N/A' }}</td>
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
        <td colspan=2><strong>Current Utilization (Inbound)</strong></td>
        <td>
          @if(isset($value['ifInOctets_perc']))
            <strong style="@if($value['ifInOctets_perc'] >= 95)color: #bd1e1e;@elseif($value['ifInOctets_perc'] >= 80)color: #ff9800;@else color: #4caf50;@endif">
              {{ number_format($value['ifInOctets_perc'], 2) }}%
            </strong>
          @else
            N/A
          @endif
        </td>
    </tr>
    @if(isset($value['ifInOctets_perc']))
    <tr>
        <td colspan=3 style="padding: 10px;">
          <div class="progress-bar-container">
            <div class="progress-bar @if($value['ifInOctets_perc'] >= 95)progress-critical @elseif($value['ifInOctets_perc'] >= 80)progress-warning @else progress-normal @endif" 
                 style="width: {{ $value['ifInOctets_perc'] }}%;">
              {{ number_format($value['ifInOctets_perc'], 2) }}%
            </div>
          </div>
        </td>
    </tr>
    @endif
    <tr>
        <td colspan=2><strong>Current Utilization (Outbound)</strong></td>
        <td>
          @if(isset($value['ifOutOctets_perc']))
            <strong style="@if($value['ifOutOctets_perc'] >= 95)color: #bd1e1e;@elseif($value['ifOutOctets_perc'] >= 80)color: #ff9800;@else color: #4caf50;@endif">
              {{ number_format($value['ifOutOctets_perc'], 2) }}%
            </strong>
          @else
            N/A
          @endif
        </td>
    </tr>
    @if(isset($value['ifOutOctets_perc']))
    <tr>
        <td colspan=3 style="padding: 10px;">
          <div class="progress-bar-container">
            <div class="progress-bar @if($value['ifOutOctets_perc'] >= 95)progress-critical @elseif($value['ifOutOctets_perc'] >= 80)progress-warning @else progress-normal @endif" 
                 style="width: {{ $value['ifOutOctets_perc'] }}%;">
              {{ number_format($value['ifOutOctets_perc'], 2) }}%
            </div>
          </div>
        </td>
    </tr>
    @endif
    <tr>
        <td colspan=2><strong>Inbound Traffic Rate</strong></td>
        <td>
          @if(isset($value['ifInOctets_rate']))
            @php
              $rate = $value['ifInOctets_rate'] * 8; // Convert bytes to bits
              if ($rate >= 1000000000) {
                echo number_format($rate / 1000000000, 2) . ' Gbps';
              } elseif ($rate >= 1000000) {
                echo number_format($rate / 1000000, 2) . ' Mbps';
              } elseif ($rate >= 1000) {
                echo number_format($rate / 1000, 2) . ' Kbps';
              } else {
                echo number_format($rate, 0) . ' bps';
              }
            @endphp
          @else
            N/A
          @endif
        </td>
    </tr>
    <tr>
        <td colspan=2><strong>Outbound Traffic Rate</strong></td>
        <td>
          @if(isset($value['ifOutOctets_rate']))
            @php
              $rate = $value['ifOutOctets_rate'] * 8; // Convert bytes to bits
              if ($rate >= 1000000000) {
                echo number_format($rate / 1000000000, 2) . ' Gbps';
              } elseif ($rate >= 1000000) {
                echo number_format($rate / 1000000, 2) . ' Mbps';
              } elseif ($rate >= 1000) {
                echo number_format($rate / 1000, 2) . ' Kbps';
              } else {
                echo number_format($rate, 0) . ' bps';
              }
            @endphp
          @else
            N/A
          @endif
        </td>
    </tr>
    <tr>
        <td colspan=3 style="padding: 10px; background-color: #f0f0f0; border-top: 2px solid #ddd;"></td>
    </tr>
        @endif
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

    <!-- UTILIZATION THRESHOLDS -->
    <tr>
        <td colspan=3 class="section-header">Utilization Reference</td>
    </tr>
    <tr>
        <td colspan=3 style="padding: 10px;">
          <table style="width: 100%; border: none;">
            <tr>
              <td style="border: none; padding: 5px;"><span style="color: #4caf50;">●</span> <strong>Normal:</strong> &lt; 79%</td>
              <td style="border: none; padding: 5px;"><span style="color: #ff9800;">●</span> <strong>Warning:</strong> 80-95%</td>
            </tr>
            <tr>
              <td style="border: none; padding: 5px;"><span style="color: #bd1e1e;">●</span> <strong>Critical:</strong> &gt; 95%</td>
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
            <li><strong>Identify traffic sources</strong> - Determine what devices or applications are consuming bandwidth</li>
            <li><strong>Check for bandwidth-heavy processes</strong> - Look for backups, file transfers, or streaming activities</li>
            <li><strong>Review QoS policies</strong> - Ensure Quality of Service is properly configured for critical traffic</li>
            <li><strong>Monitor traffic patterns</strong> - Check if utilization is sustained or temporary spike</li>
            <li><strong>Consider link upgrades</strong> - If consistently high, plan for increased capacity</li>
            <li><strong>Check for network loops</strong> - Verify Spanning Tree Protocol is functioning correctly</li>
            <li><strong>Review port configuration</strong> - Ensure port is operating at expected speed (not downgraded)</li>
            <li><strong>Implement traffic shaping</strong> - Consider rate limiting non-critical traffic</li>
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
        <td>{{ $alert->name ?? 'High Port Utilization' }}</td>
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