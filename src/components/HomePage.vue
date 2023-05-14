<template>
    <button @click="uploadImg">上传图片</button>
    <input
        type="file"
        name="file"
        id="file"
        @change="getPicture($event)"
        style="display: none"
    />
    <div class="img_show_area">
        <div id="map_test">
            <img id="bg_img" :src="coverImgUrl ? coverImgUrl : baseImg" alt="背景图" />
        </div>
    </div>
</template>

<script>
import h337 from 'heatmap.js';
export default {
    name: 'HomePage',
    data() {
        return {
            heapMapIns: null,
            pointsArr: [{ x: 115, y: 115, value: 1000 }],
            coverImgUrl: require('../assets/1.png'),
            baseImg: '',
        };
    },
    mounted() {
        window.onresize = () => {
            // this.myChart.resize();
        };
        // this.drawMap();
        console.log('do_mounted');
        this.initHeatMapOrigin();
    },
    created() {},
    methods: {
        // Methods
        // 上传本地图片
        uploadImg() {
            document.querySelector('#file').click();
        },
        // 展示本地图片
        getPicture(e) {
            //预览图片
            if (!e.target.files[0]) return; //选择了取消
            let name = e.target.files[0].name; //读取选中文件的文件名
            let size = e.target.files[0].size; //读取选中文件的大小
            console.log(name, size);
            // todo:限制上传的文件大小
            let src = window.URL.createObjectURL(e.target.files[0]);
            // this.uploadImg.push(src);
            // console.log(src);
            this.coverImgUrl = src;

            //将图片文件转化成base64格式图片
            var reader = new FileReader();
            reader.onload = e => {
                //e.target.result  就是从本地读取的图片的base64格式,将它上传给服务器即可
                //使用axios的post方法上传即可
                console.log(e, '图片上传成功');
                this.setMapTestSize();
                this.clearMap();
            };
            reader.readAsDataURL(e.target.files[0]);
        },
        // 将img的宽高赋值给包裹的div
        setMapTestSize() {
            console.log('执行css赋值');
            let imgW = document.querySelector('#bg_img').width;
            let imgH = document.querySelector('#bg_img').height;
            // console.log(imgW, imgH);

            // 赋值
            document.querySelector('#map_test').style.width = imgW + 'px';
            document.querySelector('#map_test').style.height = imgH + 'px';
        },

        initHeatMapOrigin() {
            console.log('执行初始化');
            // create configuration object
            let config = {
                container: document.getElementById('map_test'),
                radius: 50,
                // maxOpacity: 0.5,
                // minOpacity: 0,
                blur: 0.75,
                // gradient: {
                //     // enter n keys between 0 and 1 here
                //     // for gradient color customization
                //     '.5': 'blue',
                //     '.8': 'red',
                //     '.95': 'white',
                // },
            };
            // create heatmap with configuration
            this.heapMapIns = h337.create(config);

            // a single datapoint
            let point = {
                x: 100, // x coordinate of the datapoint, number
                y: 100, // y coordinate of the datapoint, number
                value: 1, // the value at datapoint(x, y)
            };
            let dataPoint = point;
            this.heapMapIns.addData(dataPoint);

            // multiple datapoint
            // (for data initialization use setData!!)
            // this.heapMapIns.addData(this.pointsArr);
        },
        // 清除已有热力图
        clearMap() {
            let data = {
                max: 100,
                min: 0,
                data: [],
            };
            this.heapMapIns.setData(data);
            console.log('Delete heatMap');
        },
    },
};
</script>

<style>
.img_show_area {
    /* width: 0; */
    height: 100%;
    background-repeat: no-repeat;
}
</style>
