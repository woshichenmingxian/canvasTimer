<template>
  <div class="canvas">
    <canvas id="canvas"></canvas>
  </div>
</template>
<script>
export default {
  data() {
    return {
        speed:0,
        secondSpeed:new Date().getSeconds()*(Math.PI/59*2),//当前的分钟,
        curHours:new Date().getHours()*(Math.PI/24)*2,
        curDay:new Date().getDay()*(Math.PI/7)*2,
        DaySpeed:0,
        hoursSpeed:0,
        test:null,
        timer:null,
        seconds:null,
        minutes:null,
        hours:null,
        week:['周日','周一','周二','周三','周四','周五','周六'].reverse(),
    }
  },
  mounted() {
    this.render()
  },
  methods: {
    async render(){
     this.seconds=this.timeData();
     this.minutes=this.timeData(58,'分');
     this.hours=this.timeData(23,'时');
     await this.init();
    
    },
    init(first){
      clearTimeout(this.timer)
      let canvas=document.querySelector('#canvas');
      let ctx=canvas.getContext('2d');
      canvas.width=parseInt(getComputedStyle(document.querySelector('.canvas'),null).width);
      canvas.height=parseInt(getComputedStyle(document.querySelector('.canvas'),null).height);
      //渐变
      let canvasBg=ctx.createLinearGradient(0, 0, canvas.width, canvas.height);
      canvasBg.addColorStop(0,'#000');
      canvasBg.addColorStop(1,'#001');
      //把他赋值给fillStyle
      ctx.fillStyle=canvasBg;
      //画图继承设置的样式
      ctx.fillRect(0,0,canvas.width, canvas.height);
      //定位画图中心
      ctx.translate(canvas.width/2, canvas.height/2);//旋转的中心点
      ctx.font = "16px serif";
      let graddient=ctx.createLinearGradient(0,0,100, 100);
      graddient.addColorStop("0","#f60");
      graddient.addColorStop("1","red");
      ctx.fillStyle=graddient;
      //周
      this.curDay=(new Date().getDay()+1)*(Math.PI/7)*2;
      if((this.DaySpeed/this.curDay).toFixed(4)!=1){
        this.DaySpeed+=(2*Math.PI/(7));
      }
      for(let i =0 ;i<this.week.length;i++){
        ctx.save();
        ctx.rotate((Math.PI/7)*i*2+this.DaySpeed);
        ctx.fillText(this.week[i], 100, 0);
        ctx.restore();
      }

      //秒
      this.secondSpeed+=(Math.PI/(59*2))
      for(let i =this.seconds.length-1 ;i>-1;i--){
        ctx.save();
        ctx.rotate((Math.PI/59)*i*2+this.secondSpeed);
        let graddient=ctx.createLinearGradient(0,0,100, 100);
        graddient.addColorStop("0","#f60");
        graddient.addColorStop("1","red");
        //把他赋值给fillStyle
        ctx.fillStyle=graddient;
        //画图继承设置的样式
        ctx.fillText(this.seconds[i], 360, 0);
        ctx.restore();
      }
      //相除tofixed取整后 判断是否为整数 ieee754 二进制对于小数位计算不友好
      if(((this.secondSpeed/(Math.PI/59*2)).toFixed(2))%1==0){
        window.cancelAnimationFrame(this.test);
        this.timer=setTimeout(()=>{
          window.requestAnimationFrame(this.init);
        },970)
      }else{
        this.test = window.requestAnimationFrame(this.init);
      }  
      
      
      //分
      let curMinutes=new Date().getMinutes();//当前的分钟
      for(let i =this.minutes.length-1 ;i>-1;i--){
        ctx.save();
        ctx.rotate(((Math.PI/59)*i*2)+(curMinutes)*((Math.PI/59)*2));
        ctx.fillText(this.minutes[i], 290, 0);
        ctx.restore();
      }
      //时
      this.curHours=new Date().getHours()*(Math.PI/24)*2;
      if((this.hoursSpeed/this.curHours).toFixed(8)!=1){
        this.hoursSpeed+=(Math.PI/24)
      }
      for(let i =this.hours.length-1 ;i>-1;i--){
        ctx.save();
        ctx.rotate((Math.PI/24)*i*2+this.hoursSpeed);
        ctx.fillText(this.hours[i], 220, 0);
        ctx.restore();
      }
      let stopNumber=2*(Math.PI/59).toFixed(2)*100;
      let nowSpeed=this.speed.toFixed(2)*100;//超出二级制最小
       
      // if(){}
      
    },
    //
    //生成日期数据
    datedata(){},
    //生成时分秒
    timeData(len=58,unit='秒'){
      let arrData=[];
      let chineseNumber=['一','二','三','四','五','六','七','八','九','十'];
      for(let i =len;i>-1;i--){ 
        if(i<10){
          arrData.push(`${chineseNumber[i]}${unit}`)
        }else{
          let arr=(i+'').split('');//[1,1]
          if(arr[1]!=9){
            arrData.push(`${chineseNumber[arr[0]-1]}十${chineseNumber[arr[1]]}${unit}`)
          }else{
            arrData.push(`${chineseNumber[arr[0]]}${chineseNumber[arr[1]]}${unit}`)
          }
           
        }
      }
      return arrData
    }
  },
}
</script>
<style lang="scss" scoped>
  .canvas{
    width: 100%;
    height: 100%;
  }
</style>
  