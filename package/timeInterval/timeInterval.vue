<template>
	<div class="time-interval col-sm-8">
		<div id="ksxz" class="row" style="border-bottom:1px solid #ccc;padding-top:15px;">
			<div class="col-sm-7 cc-ksxzbtn">
				<div class="titleText col-sm-3">
					<p class=" text-center ">Quick select</p>
				</div>
				<div  class="col-sm-9" style="padding-left: 30px;">
					<div class="timeEventBtn" v-for='quick in quickArr' v-bind:key='quick'>
						<input type="checkbox" :id="quick.id" v-model='quick.selected' :name="quick.id" @click='quickSelect(quick)' class="btn btn-default  " valus="0">
						<label :for="quick.id"> 
							<i class="fa fa-2x iconStyle" :class="quick.class"></i>
						</label>
						<p>{{quick.name}}</p>
					</div>
				</div>
			</div>
			<div class="col-sm-4 cc-ksxzlink" style="margin-left:20px;">
				<button class="btn btn-link timelink" @click='weekendSelect' timelink="timelink0" type="button">Exclude weekends</button>
				<button class="btn btn-link timelink" @click='allSelect' timelink="timelink1" type="button">Add all</button>
				<button class="btn btn-link timelink" @click='allClear'   timelink="timelink2" type="button">Clear</button>
			</div>
		</div>
		<div class="col-sm-11 cc-weekControl col-sm-offset-1">
			<div v-for='(day,index) in dayArr' v-bind:key="day">
				<div v-if='index==0'>
					<div id="hourBtn" class="col-sm-11 col-sm-offset-1">
						<div class='col-sm-12 heightLine '>
							<span class="hourNumber text-center" :class="item.class" v-for="item in hours" v-bind:key="item">{{item.name}}</span>
						</div>
					</div>
				</div>
				<div id="weekBtn" class="col-sm-1">
					<label class="checkbox-inline col-sm-12 weekText" >
						<input type="checkbox" :id="'day'+day.id" v-model='day.selected' :name="'day'+day.id" @click="selectDay(day)" value="0">
						<label :for="'day'+day.id">{{day.name}}</label>
					</label>
				</div>
				<div id='hourBtn' class='col-sm-11'>
					<div class='col-sm-12 heightLine' >
						<div class="checkboxFour" :class="hour.class"  v-for='hour in day.hours' v-bind:key="hour">
							<input type="checkbox" v-model="hour.selected" :value="hour.id" :id="'hour_'+hour.id+'_'+hour.parentId" :class="[hour.id,hour.parentId]" name="TimeDirectional" value="0" typedata="0">
							<label :for="'hour_'+hour.id+'_'+hour.parentId"></label>
						</div>
					</div>
				</div>
			</div>
		</div>
		<!--<input type="checkbox" v-model="sleNUm[1].ad"  value="0" style='visibility:visible' typedata="0">-->
		<div class="col-sm-12">
			<div class="col-sm-6">
				<span>Total：</span>
				<span class="hourCount">{{selectNum}}</span>
				<span>h</span>
				<!--<span>{{hourNum}}</span>-->
			</div>
		</div>
	</div>
</template>

<script>
	export default {
		props: ['dayArray'],
		data:function(){
			return {
					hours:[],
					weeks:['Mon.', 'Tues.', 'Wed.', 'Thur.', 'Fri.', 'Sat.', 'Sun.'],
					days:[],
					quickArr:[{'class':'fa-bed  ','name':'Dawn',id:'timeEvent0','selected':false,'min':0,'max':7},
								{'class':'fa-clock-o  ','name':'Morning',id:'timeEvent1','selected':false,'min':8,'max':11},
								{'class':'fa-coffee  ','name':'Midday',id:'timeEvent2','selected':false,'min':12,'max':14},
								{'class':'fa-cutlery  ','name':'Afternoon',id:'timeEvent3','selected':false,'min':15,'max':17},
								{'class':'fa-sun-o  ','name':'Dusk',id:'timeEvent4','selected':false,'min':18,'max':19},
								{'class':'fa-moon-o  ','name':'Night',id:'timeEvent5','selected':false,'min':20,'max':23},],
					checkAll:false,
					selectNum:0,
					sleNUm:[{'ad':false},{'ad':false}],
					
					
				}
		},
		computed: {
//hourNum:{
//  get: function () {
// 	 console.log(this.sleNUm)
//       debugger;
//     },
//     // setter
//     set: function (newValue) {
// 	  console.log(newValue);
//       debugger;
//     }
// 			},
			// hourNum:function(){
			// 	console.log(this.sleNUm)
			// 	debugger;
			// 	// var numTxt=''
			// 	// if(this.sleNUm.length==0){
			// 	// 	numTxt = ''
			// 	// }else{
			// 	// 	numTxt = this.sleNUm[0]+'h';
			// 	// }
			// 	// return numTxt;
			// },
			dayArr:function(){
				// debugger;
				var days=[]
				// console.log(this.days,'computed',this.dayArray);
					// 在没有传入时间定向的数据和没有初始化过时间定向数据的时候序列化时间定向
					if(this.days.length==0&&this.dayArray.length==0){
						for(let x=0 ; x<7 ; x++){
							let day={'id':x,name:this.weeks[x],hours:[] , 'selected': false}
							for(let i=0 ; i<24 ; i++){
								let hour={'id':'hour'+i+'day'+x,'name':''+i,'class':'hourPosition_'+i,'parentId':'day'+x , 'selected': false};
								day.hours.push(hour);
							}
							days.push(day);
						}
						this.days = days;
						console.log(days);
					}else{
						let editArr =this.dayArray
						for(let x=0 ; x<7 ; x++){
							let day={'id':x,name:this.weeks[x],hours:[] , 'selected': false}
							for(let i=0 ; i<24 ; i++){
								let hour={'id':'hour'+i+'day'+x,'name':''+i,'class':'hourPosition_'+i,'parentId':'day'+x , 'selected': false};
								hour.selected=editArr[x][i];
								day.hours.push(hour);
							}
							days.push(day);
						}
						this.days = days;
						// this.days = this.dayArray;
					}
					
				
				return this.days;
			}
		},
		created:function(){
			for(let i=0 ; i<24 ; i++){
					let hour={'id':'hourNumber_'+i,'name':i,'class':' hourPosition_'+i};
					this.hours.push(hour);
				}
			// for(let x=0 ; x<7 ; x++){
			// 	let day={'id':x,name:this.weeks[x],hours:[] , 'selected': false}
			// 	for(let i=0 ; i<24 ; i++){
			// 		let hour={'id':'hour'+i+'day'+x,'name':''+i,'class':'hourPosition_'+i,'parentId':'day'+x , 'selected': false};
			// 		day.hours.push(hour);
			// 	}
			// 	this.days.push(day);
			// }
		},
		mounted:function(){
			// this.$nextTick(()=>{
				
			// })
		},
		methods:{
			getIntervalData:function(){
				var interval=[]
				var days=this.days
				for(let x=0 ; x<7 ; x++){
					var hours=[];
					for(let i=0 ; i<24 ; i++){
						if(days[x].hours[i].selected){
							hours.push(true)
						}else{
							hours.push(false);
						}
					}
					interval.push(hours);
				}
				console.log(interval);
				return interval;
			},
			setWeekTxt:function(val){
				this.weeks=val;
			},
			selectDay:function(day){
				var vm = this;
				day.hours.map(function(item){
					item.selected=day.selected;
				});
				return day;
			},
			quickSelect:function(quick){
				var vm = this;
				this.days.map(function(day){
					var hour=day.hours;
					for(let i =0;i<hour.length;i++){
						if(i>=quick.min&&i<=quick.max){
							hour[i].selected=quick.selected;
						}
					}

				})
				
			},
			weekendSelect:function(){
			var vm = this;
				this.days.map(function(day){
					var hour=day.hours;
					if(day.id<=4){
						for(let i =0;i<hour.length;i++){
							hour[i].selected=true;
						}
					}else{
						for(let i =0;i<hour.length;i++){
							hour[i].selected=false;
						}
					}
					
				})
			},
			allSelect:function(){
				var vm = this;
				this.days.map(function(day){
					var hour=day.hours;
					for(let i =0;i<hour.length;i++){
						hour[i].selected=true;
					}
				});
			},
			allClear:function(){
				var vm = this;
				var days=this.days;
				days.map(function(day){
					var hour=day.hours;
					for(let i =0;i<hour.length;i++){
						hour[i].selected=false;
					}
				});
				this.days=days;
			},


		},
		watch:{
			// days:{
				
			// 	handler:function(val,old){
			// 		console.log('days',val[0].hours[0].selected,old[0].hours[0].selected);
			// 		if(!val.length)return;
			// 		var count=0
			// 		val.map(function(day){
			// 		var hour=day.hours;
			// 			for(let i =0;i<hour.length;i++){
			// 				if(hour[i].selected){
			// 					count++;
			// 				}
			// 			}
			// 		});
			// 		// this.selectNum=count;
			// 		// if(this.sleNUm[1].ad){
			// 		// 	this.sleNUm[1].ad=false
			// 		// }else{
			// 		// 	this.sleNUm[1].ad=true;
			// 		// }
			// 		// console.log(this.sleNUm);
			// 		console.log(this.dayArr[0].hours[0].selected);
			// 	},
				
			// 	deep:true
			// },
			dayArr:{
				handler:function(val,old){
					
					// console.log(val,old);

							if(!val.length)return;
								var count=0
								val.map(function(day){
								var hour=day.hours;
									for(let i =0;i<hour.length;i++){
										if(hour[i].selected){
											count++;
										}
									}
								});
								this.selectNum=count;
								// console.log(hour)
				},

				deep:true
			}

		}
	}
</script>

<style>
.titleText{
	padding-top: 15px;
}
.timeEventBtn input[type=checkbox]:checked + label {
  background: #ddd;

}
.timeEventBtn input{
  display: none;
}

.timeEventBtn {
    width: 58px;
    height: 55px;
    background: #fff;
    margin: 0;
    position: relative;
    display: inline-block;
    text-align: center;
	cursor: pointer;
}
.timeEventBtn label{
  border:1px solid #ccc;
  width: 36px;
  height: 36px;
  display: inline-block;
  cursor: pointer;
  z-index: 1;
  background: #999;
  color: #111111;
}
.timeEventBtn p{
  color: #59595a;
  }
 .iconStyle
 {
	/*padding: 5px;*/
	color: #fff;
	padding-top: 4px;
	padding-left: 2px;
	cursor: pointer;

 }
.hourNumber{
	width: 16px;
  	height: 16px;
	display: inline-block;
	margin:4px;
	/*padding:5px 5px;*/
}

.weekText
{
  /*border:1px solid #ccc;*/
  color: #111111;
  /* margin: 2px 4px;
  margin-left:10px; */
  padding: 0px;
}
.weekText label {
    color: #59595a;
    cursor: pointer;
}
.weekText input[type=checkbox]:checked + label {
  color: #999999;
  font-weight:bolder;
}
input[type=checkbox] {
    visibility: hidden;
}
.heightLine{
  height: 26px;
}
.checkboxFour {
  width: 16px;
  height: 16px;
  background: #ddd;
  margin: 4px 4px;
  display: inline-block;
  /*border:1px solid #ccc;*/
  border-radius: 100%;
  position: relative;
  -webkit-box-shadow: 0px 1px 3px rgba(0,0,0,0.5);
  -moz-box-shadow: 0px 1px 3px rgba(0,0,0,0.5);
  box-shadow: 0px 1px 3px rgba(0,0,0,0.5);
  vertical-align: middle;
}
/**
 * Create the checkbox button
 */
.checkboxFour label {
  display: block;
  width: 14px;
  height: 14px;
  /*border:1px solid #ccc;*/
  border-radius: 10px;
 
  -webkit-transition: all .2s ease;
  -moz-transition: all .2s ease;
  -o-transition: all .2s ease;
  -ms-transition: all .2s ease;
  transition: all .2s ease;
  cursor: pointer;
  position: absolute;
  top: 1px;
  left: 1px;
  z-index: 1;
  background: #aaa;
 
  -webkit-box-shadow:inset 0px 1px 3px rgba(0,0,0,0.5);
  -moz-box-shadow:inset 0px 1px 3px rgba(0,0,0,0.5);
  box-shadow:inset 0px 1px 3px rgba(0,0,0,0.5);
}
.checkboxFour input[type=checkbox]:checked + label {
  background: #26ca28;
}

.hourPosition_7,.hourPosition_11,.hourPosition_14,.hourPosition_17,.hourPosition_19,.hourPosition_23{
	margin-right: 20px;
}

.time-interval{
	border: 1px solid #e5e5e5;
}
</style>