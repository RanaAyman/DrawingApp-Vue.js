<template>
<body>
<div class="type">
	
	<input type="radio" name="tool" id="ic-1" @click="earser">
	<label for="ic-1" class="icon-box">
		<i class="fas fa-eraser"></i>
		<span class="name">Eraser</span>
	</label>
	
	<input type="radio" name="tool" id="ic-2" @click="isBrushTool">
	<label for="ic-2" class="icon-box">
		<i class="fas fa-paint-brush"></i>
		<span class="name">Brush</span>
	</label>
	

  <input type="color" id="ic-14" name="strokeColor" v-model="strokeColor">
	<label for="ic-14" class="icon-box">
		<i class="fas fa-eye-dropper"></i>
	</label>

	<br>
	<input type="color" id="ic-15" name="bgColor" v-model="bgColor">
	<label for="ic-15" class="icon-box">
		<i class="fas fa-fill-drip">
  </i>
	</label>
	
  <br>
	<div class="sizeIcon">Size:px
        <input class="sizeBox" type="number" min="10" max="100" step="10" v-model.number="lineWidth">
      </div>
  <br>
  <input type="radio" name="tool" id="ic-7" @click="isLineSegmentTool">
	<label for="ic-7" class="icon-box">
		<i class="fas fa-minus"></i>
		<span class="name">Line</span>
	</label> 

<input type="radio" name="tool" id="ic-5" @click="isTriangleTool">
	<label for="ic-5" class="icon-box">
		<span class="fas" style='font-size:40px;'>&#9650;</span>
		<span class="name">Triangle</span>
	</label>
	
  <input type="radio" name="tool" id="ic-6" @click="isTriangleRightTool">
  <label for="ic-6" class="icon-box">
	<span class="fas" style='font-size:40px;'>&#9699;</span>
	<span class="name">Right-A</span>
	</label>
	

	<input type="radio" name="tool" id="ic-3" @click="isRectangleTool" >
	<label for="ic-3" class="icon-box active">
		<i class="fas fa-square"></i>
		<span class="name">Rectangle</span>
	</label>
  
	
	<input type="radio" name="tool" id="ic-4" @click="isCircleTool">
	<label for="ic-4" class="icon-box">
		<i class="fas fa-circle"></i>
		<span class="name">Circle</span>
	</label>

  <input type="radio" name="tool" id="ic-8" @click="isEllipseTool">
	<label for="ic-8" class="icon-box">
		<span class="fas" style='font-size:50px;'>&#11052;</span>
		<span class="name">Ellipse</span>
	</label>
	
	
	<input type="radio" name="tool" id="ic-9" @click='undo'>
	<label for="ic-9" class="icon-box">
		<i class="fas fa-undo"></i>
		<span class="name">Undo</span>
	</label>
	
	<input type="radio" name="tool" id="ic-10" @click='redo'>
	<label for="ic-10" class="icon-box">
		<i class="fas fa-redo"></i>
		<span class="name">Redo</span>
	</label>
	
	
	<input type="radio" name="tool" id="ic-11" @click='clear'>
	<label for="ic-11" class="icon-box">
		<i class="fas fa-trash"></i>
		<span class="name">Clear</span>
	</label>
	
	<input type="file" name="tool" id="image" @change="load" >
	<label for="image" class="icon-box">
		<i class="fas fa-upload"></i>
		<span class="name">Upload</span>
	</label>

   <div class="dropdown">
  <span class="icon-box"><i class="fas fa-download"></i></span>
  <div class="dropdown-content">
   <a @click='save' :href="url" download="canvas.png" @id="save" >.PNG</a>
   <a @click='save' :href="url" download="canvas.xml" @id="saveXMl" >.XML</a>
  </div>
  </div>

	
</div>
    <canvas ref="canvas" id='canvasId' @mousedown="beginDrawing" @mousemove="keepDrawing" @mouseup="stopDrawing" width="800" height="800" ></canvas>
</body>
</template>
<script>
import axios from 'axios'
export default {
  
  data(){
    return {
      x:0,
      y:0,
      isDrawing:false,
      canvas:null,
      bgColor: '#000',
      lineWidth:10,
      strokeColor:'#000',
      tempStroke:'#000',
      tempBG:'#000',
      isBrush: true,
      isEraser: false,
      isShape:false,
      isCircle:false,
      isEllipse:false,
      isrectangle:false,
      isSegment:false,
      isTriangle:false,
      isTriangleRight:false,
      canvasArray: [],
      step: -1,
      url:'',
      arr:[],
      isclear:false
      }
   },
   mounted() {
    var c = document.getElementById("canvasId");
    this.canvas = c.getContext('2d');
    c.width = window.innerWidth;
    c.height = window.innerHeight;
    this.pushCanvas();
  },
  methods: {
    
    drawLine(x1, y1, x2, y2) {
      let ctx = this.canvas
      ctx.beginPath()
      ctx.strokeStyle = this.strokeColor
      ctx.lineWidth = this.lineWidth
      ctx.lineCap = "round";
      if(this.isBrush|| this.isEraser){
        ctx.moveTo(x1, y1)
        ctx.lineTo(x2, y2)
      }
      if(this.isShape){
        this.tempStroke = this.strokeColor;
        this.tempBG = this.bgColor;
        if(this.isCircle){
          var radius = Math.sqrt(Math.pow((x2 - x1), 2) + Math.pow((y2 - y1), 2));

          this.arr = [x1,y1,radius,this.lineWidth,this.strokeColor,this.bgColor,"","Ellipticals","Circle"]
          axios.get("http://localhost:8085/api/draw", {params: {arr: this.arr+''}})
            .then(res => console.log(res))
          
          ctx.arc(x1, y1, radius, 0, 2 * Math.PI);   
        } else if(this.isRectangle){

          this.arr = [x1,y1,x2-x1,y2-y1,this.lineWidth,this.strokeColor,this.bgColor,"","Polygons","Rectangle"]
          axios.get("http://localhost:8085/api/draw", {params: {arr: this.arr+''}})
            .then(res => console.log(res))

          ctx.fillRect(x1, y1, x2-x1, y2-y1);
        }else if(this.isEllipse){
          this.arr = [x1,y1,x2-x1,y2-y1,"0",this.lineWidth,this.strokeColor,this.bgColor,"","Ellipticals","Ellipse"]
          axios.get("http://localhost:8085/api/draw", {params: {arr: this.arr+''}})
            .then(res => console.log(res))

          ctx.ellipse(x1, y1, x2-x1, y2-y1, 0, 0, 2 * Math.PI );
        } else if(this.isSegment){
          this.arr = [x1,y1,x2,y2,this.lineWidth,this.strokeColor,this.bgColor,"","Segments","lineSegment"]
          axios.get("http://localhost:8085/api/draw", {params: {arr: this.arr+''}})
            .then(res => console.log(res))

          ctx.moveTo(x1, y1)
          ctx.lineTo(x2, y2)
          ctx.lineTo(x1, y1)
          ctx.closePath();
        } else if(this.isTriangle){
          this.arr = [x1,y1,x2,y2,x1 - (x2-x1),y2,this.lineWidth,this.strokeColor,this.bgColor,"","Polygons","Triangle"]
          axios.get("http://localhost:8085/api/draw", {params: {arr: this.arr+''}})
            .then(res => console.log(res))
          ctx.moveTo(x1, y1)
          ctx.lineTo(x2, y2)
          ctx.lineTo(x1 - (x2-x1), y2)
          ctx.closePath();
        } else if (this.isTriangleRight){
          this.arr = [x1,y1,x2,y2,x1,y2,this.lineWidth,this.strokeColor,this.bgColor,"","Polygons","Triangle"]
          axios.get("http://localhost:8085/api/draw", {params: {arr: this.arr+''}})
            .then(res => console.log(res))
          ctx.moveTo(x1, y1)
          ctx.lineTo(x2, y2)
          ctx.lineTo(x1, y2)
          ctx.closePath();
        }
        ctx.fillStyle = this.bgColor;
        ctx.fill(); 
      }
      ctx.stroke();
    },

    beginDrawing(e) {
      this.x = e.offsetX
      this.y = e.offsetY
      this.isDrawing = true
    },

   keepDrawing(e) {
     if(this.isBrush|| this.isEraser){
      if (this.isDrawing === true) {
        this.drawLine(this.x, this.y, e.offsetX, e.offsetY)
        this.x = e.offsetX
        this.y = e.offsetY
      }
    }},
    stopDrawing(e) {
      if (this.isDrawing === true) {
        this.drawLine(this.x, this.y, e.offsetX, e.offsetY)
        this.x = 0
        this.y = 0
        this.isDrawing = false
        this.pushCanvas();
      }if(this.isBrush|| this.isEraser){
        this.arr = ["0","0","0","0","0","#FFFFFF","#FFFFFF","","Segments","lineSegment"]
          axios.get("http://localhost:8085/api/draw", {params: {arr: this.arr+''}})
            .then(res => console.log(res))
      }
    },
    isLineSegmentTool(){
      this.handleColors();
      this.stroke = this.tempStroke;
      this.fillStyle = this.tempBG;
      this.isShape = true;
      this.isSegment = true;
      this.isTriangle = false;
      this.isTriangleRight = false;
      this.isCircle = false;
      this.isEllipse = false;
      this.isBrush = false;
      this.isEraser = false;
      this.isRectangle = false;
      this.strokeColor = this.tempStroke;
      this.bgColor = this.tempBG;
    },
    isEllipseTool(){
      this.handleColors();
      this.stroke = this.tempStroke;
      this.fillStyle = this.tempBG;
      this.isShape = true;
      this.isEllipse = true;
      this.isBrush = false;
      this.isEraser = false;
      this.isSegment = false;
      this.isTriangle = false;
      this.isTriangleRight = false;
      this.isCircle = false;
      this.isRectangle = false;
      this.strokeColor = this.tempStroke;
      this.bgColor = this.tempBG;
    },
    isRectangleTool(){
      this.handleColors();
      this.stroke = this.tempStroke;
      this.fillStyle = this.tempBG;
      this.isShape = true;
      this.isBrush = false;
      this.isEraser = false;
      this.isRectangle = true;
      this.isSegment = false;
      this.isTriangle = false;
      this.isTriangleRight = false;
      this.isCircle = false;
      this.isEllipse = false;
      this.strokeColor = this.tempStroke;
      this.bgColor = this.tempBG;
    },
    isTriangleTool(){
      this.handleColors();
      this.stroke = this.tempStroke;
      this.fillStyle = this.tempBG;
      this.isShape = true;
      this.isBrush = false;
      this.isEraser = false;
      this.isRectangle = false;
      this.isSegment = false;
      this.isTriangle = true;
      this.isTriangleRight = false;
      this.isCircle = false;
      this.isEllipse = false;
      this.strokeColor = this.tempStroke;
      this.bgColor = this.tempBG;
    },
    isTriangleRightTool(){
      this.handleColors();
      this.stroke = this.tempStroke;
      this.fillStyle = this.tempBG;
      this.isShape = true;
      this.isTriangleRight = true;
      this.isBrush = false;
      this.isEraser = false;
      this.isRectangle = false;
      this.isSegment = false;
      this.isTriangle = false;
      this.isCircle = false;
      this.isEllipse = false;
      this.strokeColor = this.tempStroke;
      this.bgColor = this.tempBG;
    },
    isCircleTool(){
      this.handleColors();
      this.stroke = this.tempStroke;
      this.fillStyle = this.tempBG;
      this.isShape = true;
      this.isTriangle = false;
      this.isTriangleRight = false;
      this.isCircle = true;
      this.isBrush = false;
      this.isEraser = false;
      this.isRectangle = false;
      this.isSegment = false;
      this.isEllipse = false;
      this.strokeColor = this.tempStroke;
      this.bgColor = this.tempBG;
    },
    isBrushTool(){
      this.stroke = this.tempStroke;
      this.fillStyle = this.tempBG;
      this.isBrush = true;
      this.isEraser = false;
      this.isShape = false;
      this.isRectangle = false;
      this.isSegment = false;
      this.isTriangle = false;
      this.isTriangleRight = false;
      this.isCircle = false;
      this.isEllipse = false;
      this.strokeColor = this.tempStroke;
      this.bgColor = this.tempBG;
    },
    handleColors(){
      if(!this.isEraser){
        this.tempStroke = this.strokeColor;
         this.tempBG = this.bgColor;
      }
    },
    clear(){
      this.canvas.clearRect(0, 0, this.canvas.canvas.width, this.canvas.canvas.height);
      axios.get("http://localhost:8085/api/clear")
          .then(res => console.log(res))
      this.step++;
    },
    earser(){
      this.isEraser = true;
      this.isBrush = false;
      this.isShape = false;
      this.isRectangle = false;
      this.isSegment = false;
      this.isTriangle = false;
      this.isTriangleRight = false;
      this.isCircle = false;
      this.isEllipse = false;
      this.tempStroke = this.strokeColor;
      this.tempBG = this.bgColor;
      this.strokeColor = '#f7f1f1';
      this.bgColor = '#f7f1f1';
    },
    undo() {
      if (this.step > 0) {
        this.step--;
        let canvasPic = new Image();
        canvasPic.src = this.canvasArray[this.step];
        canvasPic.onload = () => {
          this.canvas.clearRect(0, 0, this.canvas.canvas.width, this.canvas.canvas.height);
        this.canvas.drawImage(canvasPic, 0, 0);
        }
        axios.get("http://localhost:8085/api/undo")
          .then(res => console.log(res))

      }
      console.log('undo step', (this.step +1)+ ":" + ' length', this.canvasArray.length);
    },
    redo() {
    if (this.step < this.canvasArray.length-1) {
        this.step++;
        var canvasPic = new Image();
        canvasPic.src = this.canvasArray[ this.step];
        var can = this.canvas;
        canvasPic.onload = function () { can.drawImage(canvasPic, 0, 0); }
        console.log('redo step', this.step + ":" + ' length', this.canvasArray.length);
        axios.get("http://localhost:8085/api/redo")
          .then(res => console.log(res))
       }
    },
    pushCanvas() {
      this.step++;
      console.log('push step', this.step + ":" + ' length', this.canvasArray.length);
      if (this.step < this.canvasArray.length) { this.canvasArray.length = this.step; }
       this.canvasArray.push(document.getElementById("canvasId").toDataURL());
    },
    save(){
    let data =this.canvasArray[this.step];
    this.url =data;
    axios.get("http://localhost:8085/api/save")
          .then(res => console.log(res))
    },
    load(e){
            var self = this;
            var reader, files = e.target.files;
             reader = new FileReader();
            reader.onload = (e) => {
                var img = new Image();
                img.onload = function() {
                    self.drawCanvasImage(img)
                }
                img.src = e.target.result;
            };
            reader.readAsDataURL(files[0]);
            //to get the name of the uploaded file 
             var fullPath = document.getElementById('image').value;
             if (fullPath) {
             var startIndex = (fullPath.indexOf('\\') >= 0 ? fullPath.lastIndexOf('\\') : fullPath.lastIndexOf('/'));
             var filename = fullPath.substring(startIndex);
             if (filename.indexOf('\\') === 0 || filename.indexOf('/') === 0) {
             filename = filename.substring(1);
             }
             }
             axios.get("http://localhost:8085/api/load", {params: {saveName : filename}})
              .then(res => console.log(res))
             console.log('FileName : '+filename+' & path : '+fullPath );
    },
     drawCanvasImage(img) {
            var canvas = this.$refs.canvas;
              canvas.width = window.innerWidth;
              canvas.height = window.innerHeight;
            var ctx = canvas.getContext('2d');
            ctx.drawImage(img,0,0);
        },
 }
}


</script>


<style scoped>
@import url(https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.7.0/css/font-awesome.min.css);
  * {
    padding:0;
    }
  body {
    position: fixed;
    margin-top: 0;
    background-color: #333;
    color: rgb(0, 0, 0);
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    font-family: Arial, Helvetica, sans-serif;
    min-height: 100vh;
    margin: 0;
    overflow: hidden;
  }
  
  #canvasId {
    cursor: crosshair;
    background:rgb(243, 241, 241);
    border-radius: 5px;
    border: 5px solid rgb(255, 255, 255);

  }

  div::-webkit-scrollbar {
    width: 0.1em;
}
input[type="radio"] {
  display: none;
}
.type {
  height: 92%;
  width: 85px;
  position: fixed;
  z-index: 1;
  top: 0;
  left: 0;
  font-size: 15px;
  background-color: rgb(255, 255, 255);
  overflow-x: hidden;
  align-items: center;
  text-align: center;
  justify-content: space-between;
  margin: 25px;
  margin-left: 20px;
  border-radius: 90px;
  border: 5px solid #253598;
  vertical-align: middle;
  padding-top: 15px;
}
.icon-box {
  text-align: center;
}
.icon-box .fas {
  color: #253598;
  opacity: 0.5;
  transition: 500ms all;
  font-size: 2em;
  display: block;
  position: relative;
  top: 0px;
  overflow: hidden;
}
.icon-box .fas::after {
  transition: 500ms all;
  content: '';
  position: absolute;
  left: -50%;
  top: 100%;
  background: #fff;
  transform: rotate(0deg);
  width: 200%;
  height: 200%;
}
.icon-box .name {
  transition: 500ms all;
  position: relative;
  top: 0;
  overflow: hidden;
  display: block;
  color: #253598;
  font-weight: 600;
}
.icon-box .name::after {
  transform: rotate(10deg);
  transition: 500ms all;
  content: '';
  position: absolute;
  left: -75%;
  top: -100%;
  background: #fff;
  width: 200%;
  height: 400%;
}
input:checked + label .fas {
  top: -20px;
}
input:checked + label .fas::after {
  top: -10%;
  transform: rotate(30deg);
}
input:checked + label .name {
  top: -15px;
}
input:checked + label .name::after {
  transform: rotate(0deg);
  top: 300%;
}
input:checked + label .dot::after {
  width: 8px;
  height: 8px;
}
.bottom {
  width: 100%;
  text-align: center;
  position: fixed;
  bottom: 10px;
  color: #fff;
}
.bottom a {
  color: #fff;
}
input[type="color"] {
  position: absolute;
  border: transparent;
  cursor: pointer;
  visibility: hidden;
}

[type="file"] + label {
  border: none;
  color: rgb(0, 0, 0);
  cursor: pointer;
  display: inline-block;
  outline: none;

}
[type="file"]{
 display: none;
}

.dropbtn {
  padding: 16px;
  font-size: 16px;
  border: none;
  cursor: pointer;
}

.dropdown {
  position: relative;
}

.dropdown-content {
  display: none;
  position: absolute;
  background-color: #ffffff;
  z-index: 1;
}

.dropdown-content a {
  color: #253598;
  font-weight: 600;
  padding: 12px 16px;
  text-decoration: none;
  display: block;
}

.dropdown-content a:hover {color:black;}

.dropdown:hover .dropdown-content {
  display: block;
}

.sizeIcon{
color:#253598;
font-weight: 600;

}
.sizeBox{
 width:50px;
 border-radius: 100%;
 text-align: center;
 color:#253598;
 border:0.1px solid #253598;
}
[type="number"]{
   color:#253598;
   font-size:18px;
   font-weight: 600;
   outline: none;
}
</style>
