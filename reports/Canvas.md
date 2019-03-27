# `<Canvas>` 

## `<Canvas>`是什么
<Canvas>是HTML5标准中新增的标签元素，在HTML5标准之前就已经出现，最早由Apple的Safari浏览器引入，用于提供一组纯粹的2D绘图API，目前主流浏览器都已支持。<Canvas>相当于在HTML中嵌入了一张画布,可以用JS代码描述该区域，通过一套完整的绘图功能，从而生成动态的图形。

## `为什么选择<Canvas>`
- Canvas提供的功能更原始，适合像素处理，动态渲染和大数据量绘制
-  性能高，可以自己控制绘制过程，还能使用 WebGL
- 内存占用恒定，就是像素点个数

由于我们的产品有：大数据量、动态渲染、灵活扩展的需求，在讨论后，最终选择了Canvas

## `简单案例`

下段代码描述了在1000*500的画布上，由随机数决定每个像素点是黑色或者白色，并统计每秒能渲染的次数（fps）。经统计，fps稳定在60附近，证明canvas在处理大数据量的像素点有优秀的性能。
```
<canvas id="g" width="1000" height="500" style="border:1px solid #000">您的浏览器不支持 HTML5 canvas 标签。</canvas>
<p id="fps">FPS: N/A</p>
<script type="text/javascript">

var c=document.getElementById("g");
var ctx=c.getContext("2d");

var scr = ctx.createImageData(1000,500);
var fps = 0;

var fps_label = document.getElementById("fps");

function draw() {
	
	// Iterate through every pixel
	for (let i = 0; i < scr.data.length; i += 4) {

	  let x = (Math.random() > 0.5) ? 255 : 0;
	  
	  // Modify pixel data
	  scr.data[i + 0] = x;        // R value
	  scr.data[i + 1] = x;        // G value
	  scr.data[i + 2] = x;  // B value
	  scr.data[i + 3] = 255;      // A value
	}
	
	ctx.putImageData(scr, 0, 0);
	fps += 1;
	window.requestAnimationFrame(draw);
}

draw();

setInterval(function(){
	fps_label.innerHTML="FPS:" + fps;
	fps = 0;
}, 1000);

//setInterval(draw, 1);

</script>
```
