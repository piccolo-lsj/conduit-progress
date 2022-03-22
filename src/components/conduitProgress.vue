/*
 * @Author: 短笛 
 * @Date: 2022-01-22 14:32:31 
 * @Last Modified by:   短笛 
 * @Last Modified time: 2022-03-22 10:00:31 
 */
<template>
    <div id='body'>
        <!-- <canvas ref="conduit" id='conduit' :style='{width:width+"",height:height+""}'>您的浏览器暂不支持canvas,请更换浏览器</canvas> -->
    </div>
</template>

<script>
export default {
    name: "conduit-progress",
    props: {
        width: {
            type: Number,
            default: 200,
        },
        height: {
            type: Number,
            default: 200,
        },
        //管道进度百分比
        percent: {
            type: Number,
            default: 70,
        },
        //管道半径
        radius: {
            type: Number,
            default: 10,
        },
        //拐角半径
        cornerRadius: {
            type: Number,
            default: 70,
        },
        //管道边框颜色
        borderColor: {
            type: String,
            default: "#0CCDA3",
        },
    },
    data() {
        return {
            ctx: null,
            startX: 0,
            XLong: 0, //管道横轴长度
            YLong: 0, //管道Y轴长度
            cirLong: 0, //管道拐角长度
            allLong: 0, //总长
            needLong: 0, //停留长度
        };
    },
    methods: {
        init() {
            this.ctx.beginPath();
            this.ctx.strokeStyle = this.borderColor;
            this.ctx.lineWidth = 1;
            this.ctx.moveTo(this.startX, 0);
            this.ctx.arcTo(
                this.width,
                0,
                this.width,
                this.width,
                this.cornerRadius
            );
            this.ctx.arc(
                this.width - this.radius,
                this.height - this.radius,
                this.radius,
                0,
                Math.PI,
                false
            );
            this.ctx.arcTo(
                this.width - this.radius * 2,
                0 + this.radius * 2,
                this.startX,
                0 + this.radius * 2,
                this.cornerRadius - this.radius * 2
            );
            this.ctx.arc(
                this.startX,
                0 + this.radius,
                this.radius,
                Math.PI / 2,
                -Math.PI / 2,
                false
            );
            this.ctx.stroke();
            this.calLong();
        },
        calLong() {
            this.XLong = this.width - this.startX - this.cornerRadius;
            this.YLong = this.height - this.radius - this.cornerRadius;
            this.cirLong =
                (2 * Math.PI * (this.cornerRadius - this.radius)) / 4;
            this.allLong = this.XLong + this.YLong + this.cirLong; //管道总长
            this.needLong = (this.allLong * this.percent) / 100; //根据value计算出应该停留位置的长度

            console.log(this.needLong);
            if (this.needLong > 0) this.startAnimation();
        },
        //启动动画
        startAnimation() {
            let xTimer = null,
                nowX = this.startX;
            this.ctx.beginPath();
            this.ctx.moveTo(this.startX, 0 + this.radius);
            var grd = this.ctx.createLinearGradient(
                this.startX,
                0,
                this.width,
                this.height - this.radius
            );
            // 为渐变添加颜色，参数1表示渐变开始和结束之间的位置（用0至1的占比表示），参数2位颜色
            grd.addColorStop(0, "#0CCDA3");
            grd.addColorStop(1, "#C1FCD3");
            this.ctx.strokeStyle = grd;
            this.ctx.lineWidth = this.radius * 2;
            this.ctx.lineCap = "round";
            this.ctx.lineJoin = "round";
            xTimer = setInterval(() => {
                if (nowX >= this.XLong + this.startX) {
                    clearInterval(xTimer);
                    if (this.needLong > this.XLong) this.cirAnimation();
                    return;
                }
                nowX += 3;
                this.ctx.lineTo(nowX, 0 + this.radius);
                this.ctx.stroke();
            }, 1);
        },
        //拐角动画
        cirAnimation() {
            const _this = this;
            const overCir = this.needLong > this.XLong + this.cirLong;
            const calAngle = angle(
                this.cirLong,
                overCir ? this.cirLong : this.needLong - this.XLong
            ); //圆周率角度
            const sinAngel = Math.sin(calAngle); //得到sin(相应长度角度)
            // const cosAngel = Math.cos(calAngle)//cos(相应长度角度)
            const cirX = sinAngel * (this.cornerRadius - this.radius); //得到圆的x长度
            const overX = cirX + this.width - this.cornerRadius;
            let cirTimer = null,
                nowX = this.width - this.cornerRadius;
            cirTimer = setInterval(() => {
                if (nowX >= overX) {
                    if (overCir) this.downAnimation();
                    clearInterval(cirTimer);
                    return;
                }
                nowX += 1;
                this.ctx.beginPath();
                this.ctx.lineCap = "round";
                this.ctx.moveTo(nowX - 0.5, calY(nowX - 0.5));
                this.ctx.lineTo(nowX, calY(nowX));
                this.ctx.stroke();
            }, 2);
            //根据已知长度计算停留位置角度
            function angle(cirLong, cirNeedLong) {
                const NineTen = Math.PI / 2;
                return (cirNeedLong / cirLong) * NineTen;
            }
            //根据中心圆方程(已知x坐标求y轴较小坐标)
            function calY(x) {
                const cir_x_center = _this.width - _this.cornerRadius;
                const cir_y_center = _this.cornerRadius + 0;
                const r = _this.cornerRadius - _this.radius;
                const rightValue = Math.sqrt(r ** 2 - (x - cir_x_center) ** 2);
                const value = rightValue - cir_y_center;
                return Math.abs(value);
            }
        },
        downAnimation() {
            let downTimer = null,
                nowY = 0 + this.cornerRadius;
            downTimer = setInterval(() => {
                this.ctx.beginPath();
                this.ctx.lineCap = "round";
                if (
                    nowY >=
                    this.needLong -
                        this.XLong -
                        this.cirLong +
                        this.cornerRadius +
                        0
                ) {
                    clearInterval(downTimer);
                    return;
                }
                nowY += 2;
                this.ctx.lineTo(this.width - this.radius, nowY);
                this.ctx.stroke();
            });
        },
    },
    mounted() {
        this.startX = this.radius;
        this.$nextTick(() => {
            let canvasTag = document.createElement("canvas");
            canvasTag.id = "conduit";
            canvasTag.innerHTML = "您的浏览器暂不支持canvas,请更换浏览器";
            canvasTag.width = this.width + "";
            canvasTag.height = this.height + "";
            document.getElementById("body").appendChild(canvasTag);
            this.ctx = document.getElementById("conduit").getContext("2d");
            this.init();
        });
    },
};
</script>

<style>
</style>