---
layout: post
title:  "CHATGPT相关代码聊天完成流媒体调用请求"
color:  blue
width:   3
height:  1
date:   2023-03-30
categories: javascript Code
excerpt_separator: <!--more-->
---
CHATGPT常用语法<!--more-->
CHATGPT

### js 流媒体stream模式请求chatgpt,逐字打字模式返回信息.核心代码

{% highlight javascript %}
//txt是传入的问题文本字符内容
function Chatnow(txt){
//需要chataitxt的文本框,chatgpt-response的textarea文本框
	if (document.getElementById("chataitxt") && (!document.getElementById("assisttxt"))){//no need
		var assisttxt = '<textarea class="form-control" id="assisttxt"></textarea> ';
	$("#chataitxt").append(assisttxt)
	}
 document.getElementById('chataitxt').style.display='';
const chatGptUrl = 'https://api.chatgpt.com/v1/chat/completions';
const prompt = txt;
const token = 'sk-xxx';//apikey
var txtout = document.getElementById('chatgpt-response');
var txtassist = document.getElementById('assisttxt');//nn
txtassist.value =txtassist.value+"\r\n"+ txtout.value;//历史聊天记录
txtout.value = "";

const fetchCompletion = async () => {
  const response = await fetch(chatGptUrl, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'Authorization': `Bearer ${token}`,
    },
    body: JSON.stringify({
      
	   model: "gpt-3.5-turbo",
     // max_tokens: 50,//可不设置
	 	 messages: [{"role": "user", "content":prompt}],
      temperature: 0.7,//越低随机性越低
      n: 1,//换行符个数
      stream: true,
    }),
  });

  const reader = response.body.getReader();
  let result = '';
  while (true) {
    const { done, value } = await reader.read();
    if (done) break;
    const str = new TextDecoder().decode(value);
	var j=replaceAll(str,"data:","");
 j.split('\n').forEach(line => {
  var jsons = "";
  if(line!="" && line.toLowerCase().indexOf('content') !== -1){
	  try{
 jsons = JSON.parse(line);
	  }catch(error){
		  //console.log(error);
		 jsons="";
	  }
 if(jsons!="")
    result   = jsons.choices[0].delta.content;     	 
  txtout.value += result;

 
  }
});

  }
};

fetchCompletion();
}
function replaceAll(str, find, replace) {
  return str.replace(new RegExp(find, 'g'), replace);
}
function quitchat() {
 document.getElementById('chataitxt').style.display='none';
}
{% endhighlight %} 