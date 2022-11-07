---
layout: post
title:  "javascript常用语法糖,datatables初始化"
color:  blue
width:   3
height:  1
date:   2022-11-06
categories: javascript Code
excerpt_separator: <!--more-->
---
javascript常用语法<!--more-->
javascript\JQ

### Cookie设置与读取

{% highlight javascript %}
function getCookie(cname)
{
  var name = cname + "=";
  var ca = document.cookie.split(';');
  for(var i=0; i<ca.length; i++) 
  {
    var c = ca[i].trim();
    if (c.indexOf(name)==0) return c.substring(name.length,c.length);
  }
  return "";
}
//setCookie('userid','880001',999);
function setCookie(cname,cvalue,exdays)
{
  var d = new Date();
  d.setTime(d.getTime()+(exdays*24*60*60*1000));
  var expires = "expires="+d.toGMTString();
  document.cookie = cname + "=" + cvalue + "; " + expires;
}
{% endhighlight %}
##JS常用语法
#每次启动都动态加载js文件
{% highlight javascript %}
document.write("<script type='text/javascript' src='js/app.js?1004v="+Math.random()+"'><\/script>"); 
{% endhighlight %}
#获取屏幕宽度
{% highlight javascript %}
var workplacew = document.body.clientWidth;
{% endhighlight %}
#修改iframe的src链接并立即重载
{% highlight javascript %}
document.getElementById('ifr').src = "https://apis.map.qq.com/ws/location/v1/ip?ip=123.4.2.3&key=yourkeys";//js跨域iframe查询ip函数
{% endhighlight %}
#js显示隐藏控件标签
{% highlight javascript %}
 document.getElementById('nameforid').style.display=''; //显示
 document.getElementById('nameforid').style.display='none'; //隐藏
{% endhighlight %}
#js/JQ移除标签元素
{% highlight javascript %}
$("#notifydiv").remove();//移除元素
{% endhighlight %}
#js 重定向网页加随机参数
{% highlight javascript %}
 location.replace(window.location.origin+window.location.pathname+'?'+Math.random());
{% endhighlight %}
#js ajax跨域访问callback,jsonP
{% highlight javascript %}
 function jsonP({url, params}) {
    return new Promise((resolve, reject) => {
        window.callback = function(data) {
            resolve(data) // 将data传给promise的then
var ip = data.result.ip;var addr = data.result.ad_info.province + data.result.ad_info.city + data.result.ad_info.district;
console.log("数据: " + addr + "\n状态: " + status);	

            document.body.removeChild(script)
        }
        const arrs = []
        for (let key in params) {
            arrs.push(`${key}=${params[key]}`)
        }
 
        let script = document.createElement('script')
        script.src = `${url}?${arrs.join('&')}&cb=king`
        document.body.appendChild(script)
 
    })
 
}
{% endhighlight %}

#Js/JQ ajax查询BTC价格例子,Json解析,json转string,string转json
{% highlight javascript %}
function getbtcprice(){
 $.get("https://chain.so/api/v2/get_info/BTC",function(data,status){
        if(status=="success"){			
		var price= JSON.stringify(data);//json转字符
		const objprice = JSON.parse(price);//字符转json
		price = objprice.data.price;//json解析
		console.log("bTC数据: " + price + "\n状态: " + status);	

$("#txtinfo").val("BTC Price: "+ price );	//JQ修改input标签的内容
 
		}});
		
		//ajax的其他例子
		$.ajax({
    'url':'https://pincong.rocks',
     'dataType':'text',
    'success':function(json){
        console.log(json);
    }
});

 }{% endhighlight %}

#js switch函数
{% highlight javascript %}
switch(selectis) {
	case "show2":
console.log('选择 show2');
    break;
	case "show1":
console.log('选择show1');
    break;
  default:
console.log('选择其他');
}
{% endhighlight %}

#js查询域名对应IP,域名解析,类似于ping,其中有forEach循环函数用于多层json解析
{% highlight javascript %}
function qrydomain(domain){	
try{
 $.get("https://dns.alidns.com/resolve?name="+domain+"&type=1",function(data,status){
        if(status=="success"){			
var ip = "";
data.Answer.forEach(function (item) {
		ip = item.data + ' | '; 	
		})
commonUtil.message("domaininfo:" + ip );	 
		}});
}catch(err){
	commonUtil.message("domaininfoERR:" + err.message );	
}
}
{% endhighlight %}

#js弹出浮窗信息后自动关闭
{% highlight javascript %}
//commonUtil.message("info" ); //调用弹窗
var commonUtil = {
    /**
     * 弹出消息框
     * @param msg 消息内容
     * @param type 消息框类型（参考bootstrap的alert）
     */
    alert: function(msg, type){
        if(typeof(type) =="undefined") { // 未传入type则默认为success类型的消息框
            type = "success";
        }
        // 创建bootstrap的alert元素
        var divElement = $("<div id='notifydiv' style='z-index:20;margin-top:10px;'></div>").addClass('alert').addClass('alert-'+type).addClass('alert-dismissible').addClass('col-md-4').addClass('col-md-offset-4');
        divElement.css({ // 消息框的定位样式
            "position": "absolute",
            "top": "120px"
        });
        divElement.text(msg); // 设置消息框的内容
        // 消息框添加可以关闭按钮
        var closeBtn = $('<button type="button" id="notify" onclick="hidenotify()" class="close" data-dismiss="alert" aria-label="Close"><span aria-hidden="true" id="notiftspan">&times;</span></button>');
        $(divElement).append(closeBtn);
        // 消息框放入到页面中
        $('body').append(divElement);
        return divElement;
    },
    
    /**
     * 短暂显示后上浮消失的消息框
     * @param msg 消息内容
     * @param type 消息框类型
     */
    message: function(msg, type) {
        var divElement = commonUtil.alert(msg, type); // 生成Alert消息框
        var isIn = false; // 鼠标是否在消息框中
        
        divElement.on({ // 在setTimeout执行之前先判定鼠标是否在消息框中
        　　mouseover : function(){isIn = true;},
        　　mouseout  : function(){isIn = false;}
        });
 
        // 短暂延时后上浮消失
        setTimeout(function() {
            var IntervalMS = 20; // 每次上浮的间隔毫秒
            var floatSpace = 120; // 上浮的空间(px)
            var nowTop = divElement.offset().top; // 获取元素当前的top值
            var stopTop = nowTop - floatSpace;    // 上浮停止时的top值
            divElement.fadeOut(IntervalMS * floatSpace); // 设置元素淡出
            
            var upFloat = setInterval(function(){ // 开始上浮
                if (nowTop >= stopTop) { // 判断当前消息框top是否还在可上升的范围内
                    divElement.css({"top": nowTop--}); // 消息框的top上升1px
                } else {
                    clearInterval(upFloat); // 关闭上浮
                    divElement.remove();    // 移除元素
                }
            }, IntervalMS);
 
            if (isIn) { // 如果鼠标在setTimeout之前已经放在的消息框中，则停止上浮
                clearInterval(upFloat);
                divElement.stop();
            }
            
            divElement.hover(function() { // 鼠标悬浮时停止上浮和淡出效果，过后恢复
                clearInterval(upFloat);
                divElement.stop();
            },function() {
                divElement.fadeOut(IntervalMS * (nowTop - stopTop)); // 这里设置元素淡出的时间应该为：间隔毫秒*剩余可以上浮空间
                upFloat = setInterval(function(){ // 继续上浮
                    if (nowTop >= stopTop) {
                        divElement.css({"top": nowTop--});
                    } else {
                        clearInterval(upFloat); // 关闭上浮
                        divElement.remove();    // 移除元素
                    }
                }, IntervalMS);
            });
        }, 5500);
    }
}
{% endhighlight %}


## Datatables 表单配置
#html格式
{% highlight javascript %}
<table id="example" class="table table-striped table-bordered" style="width:100%" data-order='[[ 8, "desc" ]]' data-page-length='15'>
<font><a href ="#" onclick="login()" id="loginbtn" tabindex="0" aria-controls="example" style="display:none;margin-left:5px;color: #020200;" class="btn btn-outline-secondary" >登录</a></font>        
<div id="difr" style="display:none;max-height:233px;width:100%;position:sticky;z-index=11"><a href="#" style="margin-right:1px;" onclick="document.getElementById('difr').style.display='none'; ">     close    </a>
<iframe id="ifr" style="display:;max-height:100%;width:100%;" src="https://apis.map.qq.com/ws/location/v1/ip?ip=112.42.17.201&key=Y7VBZ-PTPRU-VXIV7-43QPZ-F5SV3-6RBHT" title="W3Schools Free Online Web Tutorials">
</iframe></div>
	<div class="form-group input-group">
	<select id="s1" style="display:;margin-left:1px;color: #020200;max-width:85px;">
<option value="none">选择项目</option>
<option value="qryip">IP信息查询</option>

</select>
<font><a href ="#" onclick="doselect()" id="doselect" tabindex="0" aria-controls="example" style="display:;margin-left:5px;color: #020200;" class="btn btn-outline-secondary">操作</a></font>        

 </div>
<!-- 按钮组合 -->
	<div class="form-group input-group">

		<input type="text" id="txtkey" style="z-index:10;" class="form-control" placeholder="检索信息关键字" onKeyDown="if (event.keyCode == 13) document.getElementById('billall').focus();" onchange="">
		<div class="input-group-append">
			<button id="billall" class="btn btn-outline-secondary" onclick="billall()">单据历程</button>
			<button id="billdays" class="btn btn-outline-secondary" onclick="billdays()">续费单据</button>
		</div>
	</div>


	  <thead>
            <tr>
                 
                
               
            </tr>
        </thead>
        <tbody>
            <tr>
                 
                
            </tr>
            <tr>                 
                
            </tr>
          
        </tbody>
        <tfoot>
         <tr>
		 <th>合计</th><th id="tf2" class="Sumc"></th><th style="float:left;" class="tf3">利润</th><th class="profit"></th>
		 <th style="float:right;" class="tf5">费用:</th><th class = "fee"></th><th></th><th></th>
		 <th></th><th></th><th></th><th></th>
		 <th></th><th></th><th></th><th></th>
		 <th></th><th></th><th></th>
          </tr>
        </tfoot>
    </table>
	{% endhighlight %}
	#js 初始化columns: headtittle 普通初始化
	{% highlight javascript %}
	var headtittle = [
	 {"title": "序号",data:null,"width" : '1px'},
	 { "title": "ID","data": "Id" },{ "title": "版本","data": "Ver" },{ "title": "其他","data": "VerMore" },
	 { "title": "U","data": "UserConut" ,"width" : '1px'},
	 { "title": "C","data": "UseConut" },
     { "title": "G","data": "Good" },{ "title": "日期","data": "Date" }, { "title": "最后用日","data": "LastDate" },{ "title": "地址","data": "Url" },
	 { "title": "IP地址","data": "IP" }, { "title": "pMD5","data": "Pode" },
	 { "title": "","data":null ,"visible": false}, { "title": "","data": null,"visible": false },
	  { "title": "","data": null ,"visible": false},  { "title": "","data": null ,"visible": false},
	  { "title": "","data": null,"visible": false },  { "title": "","data": null ,"visible": false},//列数只能增加不能减少
     {"data": null, "className": "center", "defaultContent": '<button type="button" id="editrow" data-toggle="modal" class="btn btn-outline btn-circle btn-sm purple"><i class="fa fa-edit"></i></button>  <button id="delrow" class="btn btn-outline btn-circle dark btn-sm black"><i class="fa fa-trash-o"></i></button>' }
 ];
 	{% endhighlight %}
 # js 初始化columns: headtittle修改表内样式
 {% highlight javascript %}
  headtittle = [{"title": "序号",data:null,"width" : '1px'},
	 {"title": "C", "data": "count" },{"title": "停", "data": "isend" ,"width" : '1px'},{ "title": "ID","data": "ID" },{ "title": "Fix","data": "Fixed" },{ "title": "Not2","data": "Not2" },{"title": "PhoneId", "data": "PhoneId" },
	 
	 
     { "title": "最后日期", "data": "lasttime" },{ "title": "使用日期","data": "date" ,render: function(data, type, row){
	var newdata = "";
	if(data.indexOf(":") != -1 ){//增加a标签点击查询ip信息
		var str = data.split(':');
		if(str[1].indexOf("|") != -1){
			var strip = str[1].split('|') ;var htmlcode = "";
			strip.forEach(function (item) { 
 htmlcode +='<font color=#739900><a href="#" onclick="qryip('+"'"+item+"'"+')">'+item+ ' </a></font>';
	 });
	      htmlcode = '<font color=#000066><a>'+str[0]+'</a></font>' +htmlcode;
		return htmlcode;}else{
			var ip = String(str[1]).replace(" ","");
			
			return '<font color=#739900><a href="#" onclick="qryip('+"'"+ip+"'"+')">' + data + '</a></font>';
		}
		}else{
			if(data.indexOf("|") != -1 ){
	var strip = data.split('|') ;var htmlcode = "";
			strip.forEach(function (item) {
    if (item.indexOf("-") != -1 ) {htmlcode += '<font color=#52527a><a>'+item+'</a></font>' }else{
 htmlcode +='<font color=#739900><a href="#" onclick="qryip('+"'"+item+"'"+')">'+item+ ' </a></font>';
	   }
   
});
		return htmlcode;
			}
			return data;
		}
		}}, 
	 { "title": "Not1","data": "Not1" },{ "title": "Hash","data": "Hash" },  { "title": "","data": null ,"visible": false},
	 { "title": "","data": null ,"visible": false}, { "title": "","data": null ,"visible": false},
	 { "title": "","data": null,"visible": false }, { "title": "","data": null,"visible": false },
	 { "title": "","data": null ,"visible": false}, { "title": "","data": null ,"visible": false},
	 {"data": null,   "render": function(data,type,row,meta){
		 var not2 = String(row.Not2);
	
return data = '<button type="button" id="editnot2id'+row.ID+'" onclick="editnot2('+row.ID+',\''+not2+'\')" class="btn btn-outline btn-circle dark btn-sm black"> 修改'+row.ID+'行:'+not2+'</button>'+
'<button type="button" id="switchid'+row.ID+'" onclick="switchwltr('+row.ID+',\''+row.isend+'\')" class="btn btn-outline btn-circle dark btn-sm black"> 修改状态'+row.ID+'行:'+row.isend+'</button>';
	 }}  
	 ];
	 {% endhighlight %}
	 #datatables 初始化代码
	  {% highlight javascript %}
	 $(document).ready(function () {
 var table=   $('#example').DataTable({
	ajax: function (data, callback, settings) {
 //封装请求参数
 var param = {};
 param.limit =data.length;//页面显示记录条数，在页面显示每页显示多少项的时候
 param.start = data.start;//开始的记录序号
 param.psw = getCookie('psw');//psw
 param.page = (data.start / data.length)+1;//当前页码
 //ajax请求数据
 $.ajax({
  type: "GET",
  url: "MainProduct/" +url ,//查历程:MainProduct/kinfo?type=bill&getbillinfo=getallbill&key= 查到期订单getbillinfo=getdayendbill
  cache: false, //禁用缓存
  data: param, //传入组装的参数
  dataType: "json",
  success: function (result) {
  //封装返回数据
  var returnData = {};
  returnData.draw = data.draw;//这里直接自行返回了draw计数器,应该由后台返回
  returnData.recordsTotal = null;//返回数据全部记录
  returnData.recordsFiltered = null;//后台不实现过滤功能，每次查询均视作全部结果
  returnData.data = result;//返回的数据列表
  var re = JSON.stringify(result);
    console.log('DataSize:'+re.length);
	if(re=='null')return;
  re = '{"data":' + re + '}';//增加属性  

commonUtil.message("查询成功 数据大小"+re.length);	
  //调用DataTables提供的callback方法，代表数据已封装完成并传回DataTables进行渲染
  //此时的数据需确保正确无误，异常判断应在执行此回调前自行处理完毕
  callback(JSON.parse(re));
  }
 });
 }, columns: headtittle
 ,order : [ [ 8, 'desc' ] ],
	//datatype : "json",
 
	//console.log(d);
	searching:true,    //去掉搜索框方法一
defaultContent  :"",
regex:true,
        bDestroy: true,
        mark: true,
        dom: 'Bfrtip',
		scrollY: 600,
        scrollX: true,
        scrollCollapse: false,
        paging: true,//分页
		responsive: true,
        autoFill: false,
		autoWidth : false,
		//destroy: true,
		select: true, 
		searchDelay: 450,//延迟搜索
		pagingType : 'simple_numbers',
		//serverSide : true,
		Retrieve: true, Processing: true, //显示加载数据时的提示
		destroy : true,
		rowReorder: {
            selector: 'td:nth-child(5)'
        },
        responsive: true,
		language: {url: 'fonts/zh.json'},lengthMenu: [[10, 25, 50, 100,500, -1],['10 rows', '25', '50', '100','500', 'Show All']],buttons: ['pageLength',{extend: 'copyHtml5',exportOptions: {columns: ':visible'}},{extend: 'excelHtml5',filename: 'myfile',exportOptions: {columns: ':visible'}},{extend: 'csvHtml5',exportOptions: {columns: ':visible'}},{extend: 'print',exportOptions: {columns: ':visible'}},{extend: 'pdfHtml5',download: 'open',exportOptions: {columns: ':visible'}},'colvis'],
		columDefs: [{
           // targets: -1,//"width": "2%",
            //visible: false
			  
            targets: [1,2,3,4],
			
            createdCell: function(cell, cellData, rowData, rowIndex, colIndex) {
                // 单元格监听doubleclick事件				
                $(cell).on('dblclick', function() {
                    // 单元格插入输入框
                    $(this).html('<input type="text" size="20" style="height: 20px" value="' + $(cell).html() + '"/>');
                    // 获取输入框
                    var aInput = $(this).find(":input");
                    // 聚焦输入框
                    aInput.focus();
                });
                   console.log('cellData:'+cellData); 
                // 单元格监听blur事件
                $(cell).on("blur", ":input", function () {
                    // 获取单元格中数据
                    var text = $(this).val();
                    // 单元格中写入数据
                    $(cell).html(text);
                    // 更新table实例中的数据
                    table1.cell(cell).data(text)
                })
            }
        
			} 
			]  ,
			 footerCallback: function (row, data, start, end, display) {//footer 写入合计内容示例
                var data = table.search();
                var ds = table.search(data).context[0].aiDisplay;
                var Sum = 0;var c = 0;var fee = 0;var billtype = '';
 
                $.each(ds, function (i, e) {
                    var data = table.row(e).data();
					billtype = String(data.类型);
					if(billtype == '费用')
					fee = Number(fee) + Number(data.收款);
				if(billtype == '销售')
                    Sum = Number(Sum) + Number(data.余利);
                    c = Number(c) + Number(data.count);
                });
								
                $(".Sumc").html(c);				
                $(".fee").html(fee);
                			
                $(".profit").html(Sum-fee);
				if(workplacew<=600){//判断屏幕宽度设置紧凑显示
					var showinfo = '利润:' + Sum + ' 费用:'+fee
					$(".tf3").html(showinfo);
				}
				if(isNaN(Sum)){
					 $(".tf3").html(Sum); //没有值就不显示了
				}
				if(isNaN(fee)){
					 $(".tf5").html(fee);
				}
				
			 
            }
            
	}
	);
	
	  //自动给第一列设置行号,第一行的data需要null
        table.on('order.dt search.dt', function () {
            table.column(0, {search:'applied', order:'applied'}).nodes().each(
               function (cell, i) {
                cell.innerHTML = i+1;
              }
            );
        }).draw();
		
		 // 监听 DT 的重绘事件
    table.on( 'draw', function () {
        //获得需要高亮的容器部分
        var body = $( table.table().body() );
        //如果之前有高亮把高亮部分去掉
        body.unhighlight();
        //根据搜索框里的关键字 高亮显示
        body.highlight( table.search() );
    } );
	
 
 
	
$('#txtkey').on( 'keyup change', function () {//自定义input搜索
    table.search( this.value ).draw();
} );
	

	
	 // $('#tf2').on('keyup change', function () {
   // oTable.column(1).search($(this).val()).draw();
// });

    // //根据footer输入框的内容进行搜索
        // table.columns().every( function () {
            // var that = this;
            // $( 'input', this.footer() ).on( 'keyup change', function () {
                // if ( that.search() !== this.value ) {
                    // that
                        // .search( this.value )
                        // .draw();
                // }
            // } );
        // } );
		
	//	  table.columns().eq(0).each(function (colIdx) {//自定义input搜索
     //   $('input', table.column(colIdx).footer()).on('keyup change', function () {
      //      table.column(colIdx).search(this.value).draw(); });});
	
	 
   
});
	{% endhighlight %}