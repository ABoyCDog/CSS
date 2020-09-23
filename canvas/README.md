# canvas 小心得

## 1、vue+canvas加载图片

+ tip： 如果直接在mounted写 img = new Image() 是不生效的，也不能说不生效，反正就是此时生成的图片没有挂载。<br>
需要在你的canvas内部写一个img,如下，虽然canvas标签内部的东西不生效，也就是图片不显示。但是挂载了一个图片节点，接下来的图片操作就可以使用这个节点
  ```js
    <canvas id="canvasImg" class="canvas-img" width="1226" height="976">
      <img ref="img" src="../../assets/marker-red.png" alt="">
    </canvas>
    ...
    mounted() {
      // 动图
      this.canvasImg = document.getElementById('canvasImg')
      this.ctxImg = this.canvasImg.getContext('2d')
      // this.img = new Image()
      // this.img.src = '../../assets/marker-red.png'
      this.img = this.$refs.img
      this.img.onload = () => {
        this.renderImg()
      }
    }
  ```
`anyway，这个方法可能不太正确，但是的确有用`

## 2、两个canvas叠加

+ 就单纯的css样式，
  ```js
    .canvas {
      position: absolute;
      top: 0;
      left: 0;
    }
  ```
+ 如果是flex布局,一般两个canvas会换行，则设margin-top: -xxx;
  ```js
    .canvas {
      margin-top: -660;
    }
  ```

## 3、画扩散圆

+ 实现原理
  通过不断的改变圆的半径大小，不断重叠达到运动的效果，在运动的过程中，设置当前canvas的透明度context.globalAlpha=0.95，使得canvas上的圆逐渐透明直至为0，从而实现这种扩散、渐变的效果。
  ```js
    // 画动态圆
    var radius = 0
    function drawCircle() {
        ctx.beginPath();
        ctx.arc(200, 535, radius, 0, Math.PI * 2);
        ctx.closePath();
        ctx.lineWidth = 2;
        ctx.setLineDash([0, 0])
        ctx.strokeStyle = 'red'
        ctx.stroke();

        radius += 0.3;
        if (radius > 66) {
            radius = 0;
        }
    }
    var render = function() {
      var prev = ctx.globalCompositeOperation;
      ctx.globalCompositeOperation = 'destination-in';
      ctx.globalAlpha = 0.95;
      ctx.fillRect(0, 0, canvas.width, canvas.height);
      ctx.globalCompositeOperation = prev;
      drawCircle();

      requestAnimationFrame(render)
    }
    render()
  ```

+ 

## 4、画椭圆

+ 使用 `ellipse` 方法替代上述画圆的 `arc` 即可，详见 [传送门](https://developer.mozilla.org/zh-CN/docs/Web/API/CanvasRenderingContext2D/ellipse)