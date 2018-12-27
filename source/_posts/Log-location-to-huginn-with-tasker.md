---
title: 使用Tasker定时记录位置到huginn
date: 2018-12-27 14:55:04
tags:
- huginn
- tasker
categories: Technology
---

# Tasker
## PROFILES
PROFILES中添加Time类型触发器，时间间隔为5分钟。关联到Huginn Locations的task。
## TASKS
创建Huginn Locations。
### Description
```
Huginn Locations (2)
A1: Get Location [ Source:GPS Timeout (Seconds):10 Continue Task Immediately:Off Keep Tracking:Off ] 
A2: Variable Split [ Name:%LOC Splitter:, Delete Base:Off ] 
A3: HTTP Post [ Server:Port:http://<server-ip>:3000/users/1/web_requests/8/supersecretstring Path: Data / File:{"latitude":%LOC1,
"longitude":%LOC2} Cookies: User Agent: Timeout:10 Content Type:application/json Output File: Trust Any Certificate:Off ] 
```
### XML
``` xml
<TaskerData sr="" dvi="1" tv="5.6">
	<Task sr="task2">
		<cdate>1405837534303</cdate>
		<edate>1545616710562</edate>
		<id>2</id>
		<nme>Huginn Locations</nme>
		<pri>6</pri>
		<Action sr="act0" ve="7">
			<code>902</code>
			<Int sr="arg0" val="0"/>
			<Int sr="arg1" val="10"/>
			<Int sr="arg2" val="0"/>
			<Int sr="arg3" val="0"/>
		</Action>
		<Action sr="act1" ve="7">
			<code>590</code>
			<Str sr="arg0" ve="3">%LOC</Str>
			<Str sr="arg1" ve="3">,</Str>
			<Int sr="arg2" val="0"/>
		</Action>
		<Action sr="act2" ve="7">
			<code>116</code>
			<Str sr="arg0" ve="3">http://<server-ip>:3000/users/1/web_requests/8/supersecretstring</Str>
			<Str sr="arg1" ve="3"/>
			<Str sr="arg2" ve="3">{"latitude":%LOC1,
"longitude":%LOC2}</Str>
			<Str sr="arg3" ve="3"/>
			<Str sr="arg4" ve="3"/>
			<Int sr="arg5" val="10"/>
			<Str sr="arg6" ve="3">application/json</Str>
			<Str sr="arg7" ve="3"/>
			<Int sr="arg8" val="0"/>
		</Action>
	</Task>
</TaskerData>
```
# Huginn
## Agent Event Flow
``` flow
st=>start: get_location
e=>end: write loc to file
op1=>operation: coordinator_translator

st->op1->e
```

## get_location
**Type:** User Location Agent
**Keep events:** Forever
**Event sources:** None
**Propagate immediately:** No
**Event receivers:** [coordinator_translator]()
**Options:**
``` json
{
  "secret": "supersecretstring",
  "max_accuracy": "",
  "min_distance": "",
  "api_key": ""
}
```
**Memory:**
``` json
{
  "last_location": {
    "latitude": 39.9005,
    "longitude": 116.343751,
    "web_request": {
      "latitude": 39.9005,
      "longitude": 116.343751
    }
  }
}
```

## coordinator_translator
**Type:** Java Script Agent
**Schedule:** Never
**Keep events:** Forever
**Event sources:** get_location
**Propagate immediately:** Yes
**Event receivers:** write loc to file
**Options:**
- **language:** JavaScript
- **code:** 坐标转换函数来自googollee的[eviltransform](https://github.com/googollee/eviltransform)
    ``` javascript
    Agent.check = function() {
        if (this.options('make_event')) {
            this.createEvent({ 'message': 'I made an event!' });
            var callCount = this.memory('callCount') || 0;
            this.memory('callCount', callCount + 1);
        }
    };

    Agent.receive = function() {
    var events = this.incomingEvents();
        for(var i = 0; i < events.length; i++) {
            this.log(JSON.stringify(events[i]))
            var gcj = wgs2gcj(events[i].payload.latitude, events[i].payload.longitude)
            this.createEvent({ 'message': 'I got an event!', 'event_was': events[i].payload, 'result': gcj });
        }
    }

    var earthR = 6378137.0;

    function outOfChina(lat, lng) {
        Agent.log(lat)
        if ((lng < 72.004) || (lng > 137.8347)) {
            return true;
        }
        if ((lat < 0.8293) || (lat > 55.8271)) {
            return true;
        }
        return false;
    }

    function transform(x, y) {
        var xy = x * y;
        var absX = Math.sqrt(Math.abs(x));
        var xPi = x * Math.PI;
        var yPi = y * Math.PI;
        var d = 20.0*Math.sin(6.0*xPi) + 20.0*Math.sin(2.0*xPi);

        var lat = d;
        var lng = d;

        lat += 20.0*Math.sin(yPi) + 40.0*Math.sin(yPi/3.0);
        lng += 20.0*Math.sin(xPi) + 40.0*Math.sin(xPi/3.0);

        lat += 160.0*Math.sin(yPi/12.0) + 320*Math.sin(yPi/30.0);
        lng += 150.0*Math.sin(xPi/12.0) + 300.0*Math.sin(xPi/30.0);

        lat *= 2.0 / 3.0;
        lng *= 2.0 / 3.0;

        lat += -100.0 + 2.0*x + 3.0*y + 0.2*y*y + 0.1*xy + 0.2*absX;
        lng += 300.0 + x + 2.0*y + 0.1*x*x + 0.1*xy + 0.1*absX;

        return {lat: lat, lng: lng}
    }

    function delta(lat, lng) {
        var ee = 0.00669342162296594323;
        var d = transform(lng-105.0, lat-35.0);
        var radLat = lat / 180.0 * Math.PI;
        var magic = Math.sin(radLat);
        magic = 1 - ee*magic*magic;
        var sqrtMagic = Math.sqrt(magic);
        d.lat = (d.lat * 180.0) / ((earthR * (1 - ee)) / (magic * sqrtMagic) * Math.PI);
        d.lng = (d.lng * 180.0) / (earthR / sqrtMagic * Math.cos(radLat) * Math.PI);
        return d;
    }

    function wgs2gcj(wgsLat, wgsLng) {
        Agent.log("wgs2gcj: " + wgsLat + ", " + wgsLng)
        if (outOfChina(wgsLat, wgsLng)) {
            return {lat: wgsLat, lng: wgsLng};
        }
        var d = delta(wgsLat, wgsLng);
        return {lat: wgsLat + d.lat, lng: wgsLng + d.lng};
    }
    ```
- **expected_receive_period_in_days:** 2
- **expected_update_period_in_days:** 2

## write loc to file
**Type:** Local File Agent
**Schedule:** Never
**Keep events:** Forever
**Event sources:** coordinator_translator
**Propagate immediately:** Yes
**Event receivers:** None
**Options:**
  - **watch:** true
  - **mode:** write
  - **path:**
    ```
    loc_history_{{ "today" | date: "%Y%m%d" }}.csv
    ```
  - **append_radio:** true
  - **append:** true
  - **data:**
    ```
    "{{ result.lat }},{{ result.lng }}",{{ result.lat }},{{ result.lng }},"{{ "now" | date: "%Y-%m-%d %H:%M:%S" }}"{% line_break %}
    ```
