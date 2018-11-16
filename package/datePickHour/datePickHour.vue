<template>
<div style="padding:10px;">
  <el-row v-show="!viewModel">
      <el-button size='small' type="primary" v-for="(week, index) in quickWEEKS" :id="index" :key="index" @click="setDayStatus">{{week}}</el-button>
  </el-row>
  <el-row :gutter="24" v-show="!viewModel">
    <el-col :span="12">
      <el-button size='small' type="primary" v-for="(part, key) in daypart" :id="key"  :key="key" @click="settimeStatus">{{ part }}</el-button>
    </el-col>
    <el-col :span="6" :offset="3">
        <div style="margin-right:10px;">
          <el-button size='small' type="success"  @click="selectAll">全选</el-button>
          <el-button size='small' type="info" @click="cleanAll">清除</el-button>
        </div>
      </el-col>
  </el-row>
  <table
    cellPadding=0
    cellSpacing=0
    class="el-date-table"
    @click="handleClick"
    @mousedown="handleKeyDown"
    @mouseup="handleKeyUp"
    @mousemove="handleMouseMove">
    <tbody style=" border: 1px solid #ebeef5;">
    <tr>
       <th  style='width:100px;text-align: center;  border: 1px solid #ebeef5;' rowspan='2' ><span>日期</span></th>
       <th  colspan='12' style="text-align: center;  border: 1px solid #ebeef5;">00:00-12:00</th>
       <th  colspan='12' style="text-align: center;  border: 1px solid #ebeef5;">12:00-24:00</th>
       <th style='width:150px;text-align: center;  border: 1px solid #ebeef5;'  rowspan='2'><span>日预算元</span></th>
    </tr>
    <tr>
       <th style=" border: 1px solid #ebeef5;" v-for="(week, key) in 24" :key="key">{{ key }}</th>
    </tr>
    <tr
      class="el-date-table__row"
      v-for="(row, key) in rows"
      :key="key">
      <td  style="padding:0;margin:0">
          <p class="PDate">{{row.date}}</p>
      </td>
      <td
        style="padding:0;margin:0"
        v-for="(cell, key) in row.hour"
        :class="getCellClasses(cell)"
        :id='cell.index'
        :key="key">
        <div class="tiemCell">
          <span>
            <!-- {{ cell.text }} -->
          </span>
        </div>
      </td>
      <td style="padding: 0 5px">
          <el-input :disabled="getbudgetStatus(row)" size='mini' v-model="row.budget" placeholder="请输入预算"></el-input>
      </td>
    </tr>
    </tbody>
  </table>
      <el-row v-show="!viewModel" :gutter="24">
        <el-col :span="10">
          <div style=" border: 1px solid #ebeef5;
                       margin-right:5px;
                       width: 25px;
                       height: 25px;
                       display:inline-block;
          "></div><span >未选</span>
          <div style=" border: 1px solid #ebeef5;
                       margin-right:5px;
                       width: 25px;
                       background-color: #f2f6fc;
                       height: 25px;
                       display:inline-block;
          "></div><span >已选</span>

         <span style="padding-left:10px;">可点击起始日期选择具体日期及时间段</span>
        </el-col>
        
        <el-col :span="6" :offset="5">
        <div style="margin-right:10px;">
        <el-button size='small' type="success"  @click="saveHandler">保存</el-button>
        <el-button size='small' type="info"  >取消</el-button>   
        </div>
      </el-col>
      </el-row>
    </div>
</template>

<script>
  import { hasClass } from 'element-ui/src/utils/dom';
  // import Locale from 'element-ui/src/mixins/locale';
  // import { arrayFindIndex, arrayFind, coerceTruthyValueToArray } from 'src/util/util';
  // import ElementUI from 'element-ui'
  const daypart = [ '全天','凌晨', '上午', '中午', '下午', '傍晚', '晚上'];
  const timeNode=[0,[4,7],[7,11],[11,14],[14,18],[18,20],[[0,4],[20,23]]]
 const quickWEEKS = [ '星期日','星期一', '星期二', '星期三', '星期四', '星期五', '星期六','工作日','周末'];
  
  const WEEKS = ['sun', 'mon', 'tue', 'wed', 'thu', 'fri', 'sat'];
  const clearHours = function(time) {
  const cloneDate = new Date(time);
  cloneDate.setHours(0, 0, 0, 0);
  return cloneDate.getTime();
  };
  const removeFromArray = function(arr, pred) {
    const idx = typeof pred === 'function' ? arrayFindIndex(arr, pred) : arr.indexOf(pred);
    return idx >= 0 ? [...arr.slice(0, idx), ...arr.slice(idx + 1)] : arr;
  };
  export default {
    // mixins: [Locale],
    model: {
      prop: 'pickerData',
      event: 'picke'
    },
    props: {
      isview:Boolean,
      isDisable:Boolean,
      pickerData:{}, 
      firstDayOfWeek: {
        default: 7,
        type: Number,
        validator: val => val >= 1 && val <= 7
      },
      value: {},
      defaultValue: {
        validator(val) {
          return val === null || isDate(val) || (Array.isArray(val) && val.every(isDate));
        }
      },
      date: {},
      selectionMode: {
        default: 'range'
      },
      showWeekNumber: {
        type: Boolean,
        default: false
      },
      disabledDate: {},
      minDate: {},
      maxDate: {},
      rangeState: {
        default() {
          return {
            endDate: null,
            selecting: false,
            row: null,
            column: null
          };
        }
      }
    },
    computed: {
      rows() {
        let rows=[];
        let selectCollection=this.selectCollection;
        let tempselectCollection=[];
        for (let i = 0; i < this.pickerData.length; i++) {
          const row = this.pickerData[i];
          let hour=[];
          let CellStatus=false;
          const item=this.pickerData[i].time;
          row.date=this.pickerData[i].date;
          row.budget=this.pickerData[i].budget;
          for (let j = 0; j < item.length; j++) {
                let cell = {};
                cell.index=j+24*(i);
                cell.rowIndex=i;
                cell.cellIndex=j;
                // 移动的时候显示选中
                if(this.hoverrCollection.length>0){
                  if((this.hoverrCollection[0][0]<=cell.rowIndex&&this.hoverrCollection[0][1]<=cell.cellIndex)&&
                  (cell.rowIndex<=this.hoverrCollection[1][0]&&cell.cellIndex<=this.hoverrCollection[1][1])){
                    cell.inRange = true;
                  }
                }
            if(selectCollection.length>0){
              for(let x =0;x<selectCollection.length;x++){
                if((selectCollection[x][0][0]<=cell.rowIndex&&selectCollection[x][0][1]<=cell.cellIndex)&&
                (cell.rowIndex<=selectCollection[x][1][0]&&cell.cellIndex<=selectCollection[x][1][1])){
                  if(selectCollection[x][0][2])
                  {
                   cell.inRange = true;
                   cell.select=true;
                   cell.isChange=true;
                  }else{
                   cell.inRange = false;
                   cell.select=false;
                   cell.isChange=false;
                  }
                }
              }
            }else{
              if(!this.donce)
              {
                if(item[j]===1){
                  if(!this.isSelect){
                  this.isSelect=true;
                  this.start=[i,j,true];
                  }
                  if(j===item.length-1){
                    this.isSelect=false;
                    this.end=[i,j-1];
                    tempselectCollection.push([this.start,this.end]);
                  }
                  cell.inRange = true;
                  cell.select=true;
                  cell.isChange=true;
                }else{
                  if(this.isSelect)
                  {
                  this.isSelect=false;
                  this.end=[i,j-1];
                  tempselectCollection.push([this.start,this.end]);
                  }
                  cell.select=0;
                }
              }else{
                  cell.select=0;
              }
            }

            if(cell.select){
              CellStatus=true;
            }
            cell.text=j;
            cell.type = 'normal';
            cell.start =this.start;
            cell.end =this.end;
            hour.push(cell);
          }
          row.CellStatus=CellStatus;
          row.hour=hour;
          rows.push(row);
        }
        if(tempselectCollection.length>0){
          this.selectCollection=[].concat(tempselectCollection);
          tempselectCollection=[];
          this.isSelect=false;
        }
        this.donce=true;
        return rows;
      },
      offsetDay() {
        const week = this.firstDayOfWeek;
        // 周日为界限，左右偏移的天数，3217654 例如周一就是 -1，目的是调整前两行日期的位置
        return week > 3 ? 7 - week : -week;
      },
      daypart(){
        return daypart;
      },
      quickWEEKS(){
        return quickWEEKS;
      },
      WEEKS() {
        const week = this.firstDayOfWeek;
        return WEEKS.concat(WEEKS).slice(week, week + 7);
      },
      year() {
        return this.date.getFullYear();
      },
      month() {
        return this.date.getMonth();
      },

      startDate() {
        return getStartDateOfMonth(this.year, this.month);
      },
    },
    watch: {
      'rangeState.endDate'(newVal) {
        this.markRange(newVal);
      },
      minDate(newVal, oldVal) {
        if (newVal && !oldVal) {
          // this.rangeState.selecting = true;
          this.markRange(newVal);
        } else if (!newVal) {
          this.rangeState.selecting = false;
          this.markRange(newVal);
        } else {
          this.markRange();
        }
      },

      maxDate(newVal, oldVal) {
        if (newVal && !oldVal) {
          this.rangeState.selecting = false;
          this.markRange(newVal);
        }
      }
    },
    data() {
      return {
        viewModel:this.isview,
        disableModel:this.isDisable,
        donce:false,
        KeyDown:false,
        selectCollection:[],
        hoverrCollection:[],
        start:0,
        end:0,
        hoverrStart:0,
        hoverrEnd:0,
        current:0,
        isSelect:false,
        tableRows:  { date: "",
        time: [ 0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0],
        budget: 0
        },
      };
    },
    methods: {
      settimeStatus(event){
        var rows = this.pickerData;
        if(event.currentTarget.id==0){
          this.selectAll();
          return;
        }
        let during=timeNode[event.currentTarget.id];
        for (let i = 0; i < rows.length; i++) {
          if(Array.isArray(during))
          {
              this.start=[i,during[0][0],true];
              this.end=[i,during[0][1]];
              this.selectCollection.push([this.start,this.end]);
              this.start=[i,during[1][0],true];
              this.end=[i,during[1][1]];
              this.selectCollection.push([this.start,this.end]);
          }else{
              this.start=[i,during[0],true];
              this.end=[i,during[1]];
              this.selectCollection.push([this.start,this.end]);
          }

        }
      },
      setDayStatus(event){
        var rows = this.pickerData;
        let WeekDay='';

        if(event.currentTarget.id<=6){
            WeekDay=Number(event.currentTarget.id);
        }else if(event.currentTarget.id==7){
            WeekDay=[1,2,3,4,5]
        }
        else if(event.currentTarget.id==8){
          WeekDay=[0,6]
        }
        for (let i = 0; i < rows.length; i++) {
          var d = moment(rows[i].date).weekday();
          if(Array.isArray(WeekDay)){
            if(WeekDay.indexOf(d)!=-1)
            {
              this.start=[i,0,true];
              this.end=[i,23];
              this.selectCollection.push([this.start,this.end]);
            }
          }else if(WeekDay==d){
            this.start=[i,0,true];
            this.end=[i,23];
            this.selectCollection.push([this.start,this.end]);
         }
        }
         

      },
      saveHandler(){
        let result=[]
        let rows=this.rows
          for (let i = 0, k = rows.length; i < k; i++) {
            let row = rows[i];
            let time=[];
            let date=row.date;
            let budget=row.budget;
            for (let j = 0, l =  row.time.length; j < l; j++) {
              time[j]=Number(row.hour[j].select);
            }
            result.push({'date':date,'budget':budget,'time':time})
          }
          this.viewModel=true;
          this.$emit('picke', result)
      },
      selectAll(){
        let maxrow=this.rows.length-1;
        this.selectCollection=[]
        this.selectCollection.push([[0,0,true],[maxrow,23]])
      },
      cleanAll(){
        this.selectCollection=[];
        this.hoverrCollection=[];
      },
      cellMatchesDate(cell, date) {
        const value = new Date(date);
        return this.year === value.getFullYear() &&
          this.month === value.getMonth() &&
          Number(cell.text) === value.getDate();
      },
      isWeekActive(cell) {
        if (this.selectionMode !== 'week') return false;
        const newDate = new Date(this.year, this.month, 1);
        const year = newDate.getFullYear();
        const month = newDate.getMonth();
        if (cell.type === 'prev-month') {
          newDate.setMonth(month === 0 ? 11 : month - 1);
          newDate.setFullYear(month === 0 ? year - 1 : year);
        }
        if (cell.type === 'next-month') {
          newDate.setMonth(month === 11 ? 0 : month + 1);
          newDate.setFullYear(month === 11 ? year + 1 : year);
        }
        newDate.setDate(parseInt(cell.text, 10));
        const valueYear = isDate(this.value) ? this.value.getFullYear() : null;
        return year === valueYear && getWeekNumber(newDate) === getWeekNumber(this.value);
      },
      getbudgetStatus(cell){
        // return true
        if(this.viewModel){ return true;}
          if(cell.CellStatus){
            return false
          }else{
            return true
          }

      },
      getCellClasses(cell) {
        let classes = [];
        if (cell.inRange && (cell.type === 'normal' )) {
          classes.push('in-range');
        }
        if (cell.selected) {
          classes.push('selected');
        }
        return classes.join(' ');
      },
      markRange(maxDate) {
        const startDate = this.startDate;
        if (!maxDate) {
          maxDate = this.maxDate;
        }
        const rows = this.rows;
        const minDate = this.minDate;
        for (let i = 0, k = rows.length; i < k; i++) {
          const row = rows[i];
          for (let j = 0, l = row.length; j < l; j++) {
            if (this.showWeekNumber && j === 0) continue;
            const cell = row[j];
            const index = i * 7 + j + (this.showWeekNumber ? -1 : 0);
            const time = nextDate(startDate, index - this.offsetDay).getTime();
            if (maxDate && maxDate < minDate) {
              cell.inRange = minDate && time >= clearHours(maxDate) && time <= clearHours(minDate);
              cell.start = maxDate && time === clearHours(maxDate.getTime());
              cell.end = minDate && time === clearHours(minDate.getTime());
            } else {
              cell.inRange = minDate && time >= clearHours(minDate) && time <= clearHours(maxDate);
              cell.start = minDate && time === clearHours(minDate.getTime());
              cell.end = maxDate && time === clearHours(maxDate.getTime());
            }
          }
        }
      },
      handleKeyDown(event){
        // console.log('handleKeyDown',event.target)
          this.KeyDown=true;
      },
      handleKeyUp(event){
        //  console.log('handleKeyUp',event.target)
          this.KeyDown=false;
      },
      handleMouseMove(event) {
        if (!this.rangeState.selecting) return;
        let target = event.target;
        if (target.tagName === 'SPAN') {
          target = target.parentNode.parentNode;
        }
        if (target.tagName === 'DIV') {
          target = target.parentNode;
        }
        if (target.tagName !== 'TD') return;
        const column = target.cellIndex;
        const row = target.parentNode.rowIndex - 2;
        if(!this.end&&this.start)
        {
         const cellIndex = target.cellIndex-1;
         const rowIndex = target.parentNode.rowIndex-2;
         this.hoverrEnd=[rowIndex,cellIndex];
         this.hoverrCollection=[this.start,this.hoverrEnd];
        }
      },
      hasContain(target){
        let cell=target;
        const item=this.tableRows;
        let selectCollection = this.selectCollection;
        let rows=[];
        let chonse=false
        for (let i = 0; i < 6; i++) {
          const row = item;
          for (let j = 0; j < item.time.length; j++) {
            if(selectCollection.length>0){
              for(let x =0;x<selectCollection.length;x++){
                if((selectCollection[x][0][0]<=cell.rowIndex&&selectCollection[x][0][1]<=cell.cellIndex)&&
                (cell.rowIndex<=selectCollection[x][1][0]&&cell.cellIndex<=selectCollection[x][1][1])){
                  if(selectCollection[x][0][2])
                  {
                      chonse = true;
                  }else{
                      chonse = false;
                  }
                }}}}}
          return chonse;
      },
      handleClick(event) {
        let target = event.target;
        if (target.tagName === 'SPAN') {
          target = target.parentNode.parentNode;
        }
        if (target.tagName === 'DIV') {
          target = target.parentNode;
        }
        if (target.tagName !== 'TD') return;
        if (hasClass(target, 'disabled') || hasClass(target, 'week')) return;
        const cellIndex = target.cellIndex-1;
        const rowIndex = target.parentNode.rowIndex-2;

        if(this.isSelect){
          this.isSelect=false;
          this.end=[rowIndex,cellIndex];
          if(this.start[0]>this.end[0])
          {
            this.start=[];
            this.end=[];
            return;
          }
          this.selectCollection.push([this.start,this.end]);
        }else{
          let node= {
              rowIndex:target.parentNode.rowIndex-2,
              cellIndex:cellIndex
          }
          let chosen = this.hasContain(node);
          this.isSelect=true;
          this.end='';
          if(chosen)
          {
            this.start=[rowIndex,cellIndex,false];
          }else{
            this.start=[rowIndex,cellIndex,true];
          }
        }
        this.rangeState.selecting = true;
        this.$nextTick(() => {
        this.handleMouseMove(event);
        });
      }
    }
  };
</script>
<style>
.tiemCell{
  border: 1px solid #ebeef5;
  height:45px;
}
.el-date-table th {
  border: solid 0 #ebeef5; 
}
.el-date-table{
  height: 270px;
  max-height: 500px;
  overflow: auto;
  /* overflow-x: auto; */
  display: inline-block;
}
.el-row {
    margin-bottom: 5px;
}

.el-form-item__content{
  line-height: 20px!important;
}
.PDate{
margin: 0;
}
</style>
