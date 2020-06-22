<template>
		<div class="datetime-picker">
			<input @click="change()" type="text"
				@blur="blurHandler($event)"
				:format="format"
				:readOnly="readOnly"
				:style="styleObj"
				v-model="showValue"/>
			<div v-show="show" class="picker-warp">
				<table class="date-picker">
					<thead>
						<tr class="date-head">
							<th colspan="4">
								<span class="btn-prev" @click="yearClick(-1)">&lt;</span>
								<span class="show-year">{{now.getFullYear()}}</span>
								<span class="btn-next" @click="yearClick(1)">&gt;</span>
							</th>
							<th colspan="3">
								<span class="btn-prev" @click="monthClick(-1)">&lt;</span>
								<span class="show-month">{{months[now.getMonth()]}}</span>
								<span class="btn-next" @click="monthClick(1)">&gt;</span>
							</th>
						</tr>
						<tr class="date-days">
							<td v-for="day in days"
								:key="day">{{day}}</td>
						</tr>
					</thead>
					<tbody>
						<tr v-for="i in 6"
							:key="i">
							<td  v-for="j in 7"
								:key="j"
							:class="date[(i - 1)*7 + j - 1] && date[(i - 1)*7 + j -1].status"
							@click="pickDate(date[(i - 1)*7 + j - 1] && date[(i - 1)*7 + j -1].time)">
							{{date[(i - 1)*7 + j - 1] && date[(i - 1)*7 + j - 1].text}}</td>
						</tr>
					</tbody>
				</table>
			</div>
		</div>
</template>

<script>
	export default {
		name : "date-picker",
		props : {
			format : {
				type: String,
				default : "YYYY-MM-DD"
			},
			readOnly: {
				type: Boolean,
				default : false
			},
			styleObj : {
				type: Object
			}
		},
		data : function() {
			return {
				show: false,
				now: new Date(),
				showValue: '',
				date : [],
				days : ["Su", "Mo", "Tu", "We", "Th", "Fr", "Sa"],
				months : ["Jan", "Feb", "Mar", "Apr", "May", "Jun", "Jul", "Aug", "Sep", "Oct", "Nvm", "Dec"]
			}
		},
		watch: {
			show: function() {
				this.update();
			},
			now : function() {
				this.update();
			}
		},
		methods: {
			close : function() {
				this.show = false;
			},
			update : function() {
				var arr = [];
				var time = new Date(this.now);
				time.setMonth(time.getMonth(), 1);
				var curFirstDay = time.getDay();
				time.setDate(0);
				if (curFirstDay !== 0) {
					var lastDay = time.getDate();
					for (let i = curFirstDay; i > 0; i--) {
						arr.push({
								text: lastDay  - i + 1,
								time : new Date(time.getFullYear(), time.getMonth(), lastDay  - i + 1),
								status : "pass-date"
							});
					}
					
				}
				time.setMonth(time.getMonth() + 2, 0);
				var curDayCount = time.getDate();
				for (let i = 0; i < curDayCount; i++) {
					arr.push({
						text: i + 1,
						time: new Date(time.getFullYear(), time.getMonth(), i),
						status : "active-date"
					});
				}
				var j = 1;
				while(arr.length < 42) {
					arr.push({
						text: j,
						status : "future-date"
					});
					j++;
				}
				this.date = arr;
			},
			pickDate: function(pickedDate) {
				this.now = pickedDate;
				this.showValue = this.parse();
				this.close();
			},
			blurHandler : function() {
				this.close();
			},
			parse : function(time = this.now, format = this.format) {
				var year = time.getFullYear();
				var month = time.getMonth() + 1;
				var date = time.getDate();
				var monthName = this.months[time.getMonth()];
				
				var map = {
					YYYY : year,
					yyyy : year,
					MMM : monthName,
					MM : ("0" + month).slice(-2),
					M : month,
					mmm : monthName,
					mm : ("0" + month).slice(-2),
					m : month,
					DD : ("0" + date).slice(-2),
					D : date,
					dd : ("0" + date).slice(-2),
					d : date
				};
				
				return format.replace(/Y+|M+|D+|y+|m+|d+/g, function(str) {
					return map[str];
				});
			},
			change : function() {
				this.show = !this.show;
			},
			yearClick: function(val) {
				this.now.setFullYear(this.now.getFullYear() + val);
				this.now = new Date(this.now);
			},
			monthClick: function(val) {
				this.now.setMonth(this.now.getMonth() + val);
				this.now = new Date(this.now);
			},
		},
	}
</script>

<style scoped>
	.datetime-picker {
		position: relative; 
		display: inline-block;
		font-family: "Segoe UI","Lucida Grande",Helvetica,Arial,"Microsoft YaHei";
		-webkit-font-smoothing: antialiased;
		color: #333;
	}
	.datetime-picker * {
		box-sizing: border-box;
	}
	.datetime-picker input {
		width: 100%;
		padding: 5px 10px;
		height: 30px;
		outline: 0 none;
		border: 1px solid #ccc;
		font-size: 13px;
	}
	.datetime-picker .picker-warp {
		width: 238px; 
		height:280px; 
		z-index: 1000;  
		background-color: #fff; 
		position: absolute;  
		z-index: 1000;
		box-shadow: 0 0 6px #ccc;
		margin-top: 2px
	}
	.datetime-picker table {
		width : 100%;
		border-collapse: collapse;
		border-spacing: 0;
		text-align: center;
		font-size: 13px;
	}
	.datetime-picker tr {
		height: 34px;
		border: 0 none;
	}
	.datetime-picker th, .datetime-picker td {
		user-select: none;
		width: 34px;
		height: 34px;
		padding: 0;
		border: 0 none;
		line-height: 34px;
		text-align: center;
	}

	.datetime-picker td {
		cursor: pointer;
	}

	.datetime-picker td:hover {
		background-color: #f0f0f0;
	}

	.datetime-picker td.pass-date, .datetime-picker td.future-date {
		color: #aaa;
	}

	.datetime-picker td.date-active {
		background-color: #ececec;
		color: #3bb4f2;
	}
	
	.datetime-picker .date-head {
		background-color: #3bb4f2;
		text-align: center;
		color: #fff;
		font-size: 14px;
	}

	.datetime-picker .date-days {
		color: #3bb4f2;
		font-size: 14px;
	}

	.datetime-picker .show-year {
		display: inline-block;
		min-width: 62px;
		vertical-align: middle;
	}

	.datetime-picker .show-month {
		display: inline-block;
		min-width: 28px;
		vertical-align: middle;
	}

	.datetime-picker .btn-prev,
	.datetime-picker .btn-next {
		cursor: pointer;
		display: inline-block;
		padding: 0 10px;
		vertical-align: middle;
	}

	.datetime-picker .btn-prev:hover,
	.datetime-picker .btn-next:hover {
		background: rgba(16, 160, 234, 0.5);
	}
</style>
