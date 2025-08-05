```dataviewjs

dv.span("** 😊 Suprabhatam  😥**") /* optional ⏹️💤⚡⚠🧩↑↓⏳📔💾📁📝🔄📝🔀⌨️🕸️📅🔍✨ 🏋️ */
const calendarData = {
	year: 2025,
	colors: {
		blue:        ["#8cb9ff", "#69a3ff", "#428bff", "#1872ff", "#0058e2"],
		green:       ["#c6e48b", "#7bc96f", "#49af5d", "#2e8840", "#196127"],
		red:         ["#ff9e82", "#ff7b55", "#ff4d1a", "#e73400", "#bd2a00"],
		orange:      ["#ffa244", "#fd7f00", "#dd6f00", "#bf6000", "#9b4e00"],
		pink:        ["#ff96cb", "#ff70b8", "#ff3a9d", "#ee0077", "#c30062"],
		orangeToRed: ["#ffdf04", "#ffbe04", "#ff9a03", "#ff6d02", "#ff2c01"]
	},
	showCurrentDayBorder: true,
	defaultEntryIntensity: 4,
	intensityScaleStart: 10,
	intensityScaleEnd: 100,
	entries: [],
}

//DataviewJS loop
for (let page of dv.pages('"CALENDER"').where(p => p.Suprabhatam)) {
	//dv.span("<br>" + page.file.name)
	calendarData.entries.push({
		date: page.file.name,
		intensity: page.Suprabhatam,
		//content: "⏳",
		color: "orange",
	})
}

renderHeatmapCalendar(this.container, calendarData)
```

```dataviewjs
dv.span("** 🏋️ Jogging 🏋️**") /* optional ⏹️💤⚡⚠🧩↑↓⏳📔💾📁📝🔄📝🔀⌨️🕸️📅🔍✨ 🏋️ */
const calendarData = {
	year: 2025,
	colors: {
		blue:        ["#8cb9ff", "#69a3ff", "#428bff", "#1872ff", "#0058e2"],
		green:       ["#c6e48b", "#7bc96f", "#49af5d", "#2e8840", "#196127"],
		red:         ["#ff9e82", "#ff7b55", "#ff4d1a", "#e73400", "#bd2a00"],
		orange:      ["#ffa244", "#fd7f00", "#dd6f00", "#bf6000", "#9b4e00"],
		pink:        ["#ff96cb", "#ff70b8", "#ff3a9d", "#ee0077", "#c30062"],
		orangeToRed: ["#ffdf04", "#ffbe04", "#ff9a03", "#ff6d02", "#ff2c01"]
	},
	showCurrentDayBorder: true,
	defaultEntryIntensity: 4,
	intensityScaleStart: 10,
	intensityScaleEnd: 100,
	entries: [],
}

//DataviewJS loop
for (let page of dv.pages('"CALENDER"').where(p => p.Jogging)) {
	//dv.span("<br>" + page.file.name)
	calendarData.entries.push({
		date: page.file.name,
		intensity: page.Jogging,
		//content: "⏳",
		color: "red",
	})
}

renderHeatmapCalendar(this.container, calendarData)
```

```dataviewjs
dv.span("**✨ Mandir ✨**") /* optional ⏹️💤⚡⚠🧩↑↓⏳📔💾📁📝🔄📝🔀⌨️🕸️📅🔍✨ 🏋️ */
const calendarData = {
	year: 2025,
	colors: {
		green:       ["#c6e48b", "#7bc96f", "#49af5d", "#2e8840", "#196127"],
	},
	showCurrentDayBorder: true,
	defaultEntryIntensity: 4,
	intensityScaleStart: 10,
	intensityScaleEnd: 100,
	entries: [],
}

//DataviewJS loop
for (let page of dv.pages('"CALENDER"').where(p => p.Mandir)) {
	//dv.span("<br>" + page.file.name)
	calendarData.entries.push({
		date: page.file.name,
		intensity: page.Mandir,
		//content: "⏳",
		color: "green",
	})
}

renderHeatmapCalendar(this.container, calendarData)
```

```dataviewjs
dv.span("**💤 Night Lab 💤**") /* optional ⏹️💤⚡⚠🧩↑↓⏳📔💾📁📝🔄📝🔀⌨️🕸️📅🔍✨ 🏋️ */
const calendarData = {
	year: 2025,
	colors: {
		blue:        ["#8cb9ff", "#69a3ff", "#428bff", "#1872ff", "#0058e2"],
	},
	showCurrentDayBorder: true,
	defaultEntryIntensity: 4,
	intensityScaleStart: 10,
	intensityScaleEnd: 100,
	entries: [],
}

//DataviewJS loop
for (let page of dv.pages('"CALENDER"').where(p => p.NightLab)) {
	//dv.span("<br>" + page.file.name)
	calendarData.entries.push({
		date: page.file.name,
		intensity: page.NightLab,
		//content: "⏳",
		color: "blue",
	})
}

renderHeatmapCalendar(this.container, calendarData)
```

`![[Pasted image 20250116101539.png]]`
