# vue-timepicker
基于Vue的时间选择器 | A time picker based on vuejs

# demo

![demo](http://cdn.szunews.com/opensource/demo/time_picker.gif)

# How to use

HTML:
```html
<time-picker :microtime.sync="time"></time-picker>
```

js:
```javascript
import timePicker from './components/timepicker'
...
	data () {
		return {
			time: 0
		}
	},
	components: {
        dataSelector
    }
...
```

# Notice

In order to support backend(such as PHP) to parse timestamp. The length of `microtime` is 10.