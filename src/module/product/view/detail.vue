<template>
    <div class="table-title">产品详情<a role="button" class="back"><i class="icon-angle-left"></i>返回列表</a></div>
	<div class="sub-title">
		[ 产品信息 ]
	</div>
	<ul class="detail-list">
	    <li>
            <span>产品ID :</span><span>{{list.id}}</span>
        </li>
		<li>
		    <span>产品名称 :</span><span>{{list.name}}</span>
		</li>
		<li>
            <span>产品行业 :</span><span>{{list.industry}}</span>
        </li>
        <li>
            <span>产品类别 :</span><span>{{list.category}}</span>
        </li>
        <li>
            <span>当前产品进度 :</span><span>{{list.process}}</span>
        </li>
        <li>
            <span>产品简介 :</span><span>{{list.desc}}</span>
        </li>
        <li>
            <span>用户ID :</span><span>{{list.user_id}}</span>
        </li>
	</ul>
	<div class="sub-title">
        [ 技术参数 ]
    </div>
    <ul class="detail-list">
        <li>
            <span>操作系统 :</span><span>{{list.os}}</span>
        </li>
        <li>
            <span>网络运营商 :</span><span>{{list.carrier}}</span>
        </li>
        <li>
            <span>联网方式 :</span><span>{{list.connection}}</span>
        </li>
        <li>
            <span v-if="post.page='plist'">接入协议 :</span><span v-else>设备接入方式 :</span>
            <span>{{list.protocol}}</span>
        </li>
        <li>
            <span>接入设备数 :</span><span>{{list.device_total}}</span>
        </li>
        <li>
            <span>创建时间 :</span><span>{{list.create_time}}</span>
        </li>
    </ul>
    <!--<hr>
    <div class="table-title">html5包</div>
    <ul class="detail-list">
        <li>
            <span>ZIP包 :</span>
            <span><a href="http://upfiles.heclouds.com/123/ueditor/2016/07/08/xxx.zip">http://upfiles.heclouds.com/123/ueditor/2016/07/08/xxx.zip</a></span>
            <span class="auditing">审核通过</span>
        </li>
        <li>
            <span>H5访问地址 :</span><span><a href="http://upfiles.heclouds.com/123/ueditor/2016/07/08/xxx/index.html">http://upfiles.heclouds.com/123/ueditor/2016/07/08/xxx/index.html</a></span>
        </li>
    </ul>-->
</template>
<script>
    export default {
      data() {
        return {
            list: {
            },
        }
      },
      components: {
      },
      ready() {
          let _this = this;
          $.ajax({
              url: _this.post.url,
              type: 'get',
              dataType: 'json',
              data: {
                  type: _this.post.type,
                  id:  _this.post.id
              },
              success: function(res) {
                  if(res.code != 0) {
                      console.log(res.msg);
                      return
                  }
                  for(let i in res.data) {
                      _this.$set('list.'+i, res.data[i]);
                  }
              },
          })
      },
      methods: {
      },
      props: ['post'],
    }
</script>
<style scoped>
.back {
    float: right;
    font-size: 12px;
    color: #19bbff;
}
.back:hover {
    color: #41dbff;
}
.back>i {
    margin-right: 10px;
}
.sub-title {
    color: #333;
    margin-left: 30px;
    line-height: 30px;
}
.detail-list li {
    line-height: 35px;
    white-space: nowrap;
}
.detail-list li span:nth-child(1) {
    display: inline-block;
    width: 160px;
    margin-right: 5px;
    text-align: right;
}
.auditing {
    margin-left: 20px;
    color: #2cd188;
}
</style>