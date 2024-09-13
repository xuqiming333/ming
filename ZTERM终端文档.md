#如何本地使用ZTERM
1.在终端下进入到 zterm_Web_Xterm\release文件夹下执行./witty run bash命令
2.打开浏览器输入本地的IP:8080/login进入登录页面
#如何修改ZTERM之后重新编译
每当修改了内容时候都需要执行一次这个命令
1.进入zterm_Web_Xterm文件夹，执行go build -o ./release/witty命令
2.打开浏览器进入 IP:8080/login,(刷新一下）
#前端如何跟后端交互的
文件位置：跟后端交互的文件在 zterm_Web_Xterm\assets\type.js里
什么是async和await:await 关键字只能在异步函数内部使用，它可以放在一个返回 Promise 的表达式前面，暂停异步函数的执行，直到该 Promise 对象变为 resolved 状态并返回结果。使用 await 关键字可以使异步代码看起来像同步代码，更易于理解和编写。
什么是axios：它的作用是通过一个后端接口来请求服务器的数据之后通过结合promise的.then方法获取到服务器的数据，它返回一个promise对象，promise对象通过.then方法可以获取到服务器传递过来的数据。
前端跟后端交互主要用了axios获取服务端的数据之后和promise结合async，await来让代码异步同步化(也就是说我在执行let res=await getData()方法时候后面的代码会暂停执行，等到服务器返回数据之后才会执行后续代码,axios.get()方法返回一个promise对象，这个promise对象通过.then(res=>{res}) res就是后端返回的数据对象 )。
1.主要关注createRobotChatBox()，getData()这两个方法
##getData()方法的作用及原理
  1.const rb_content=document.querySelectorAll('.robot_content')，const textarea=document.querySelector('.textarea');这两个代码是获取DOM用的
  这个'https://apis.tianapi.com/zhanan/index'就是后端提供的接口地址， 
  params: {  
      key:textarea.textContent
  }
   params是参数，这个key就是参数名字，textarea.textContent就是用户输入框里的内容(当用户按下回车或者点击搜索按钮时候获取到的输入框里的内容，是一个字符串比如 输入框里输入你好，那么textarea.textContent就是你好)
   这个参数就是用户在输入框输入的内容之后通过axios发送请求时把参数内容传递给后台
   return Promise.resolve(res) 如果服务器返回数据回来之后将res也就是数据对象用Promise包裹一下然后返回，然后通过await 来修饰这个函数let res=await getData()，这个res就是服务器返回的数据对象
   //模拟服务器获取数据
  function getData(){
    const rb_content=document.querySelectorAll('.robot_content')
    const textarea=document.querySelector('.textarea');
    //利用axios通过接口地址获取数据
    return axios.get('https://apis.tianapi.com/zhanan/index',{
        params: {  
          key:textarea.textContent
        }       
    }).then((res)=>{
      //清除thinking...内容
      rb_content[rb_content.length - 1].textContent=''  
      return Promise.resolve(res)
    }).catch((err)=>{
      //清除thinking...内容
      rb_content[rb_content.length - 1].textContent=''  
      return Promise.reject(err)
    })
  }
  2.createRobotChatBox()这个方法里只关注
   res就是通过axios请求服务器返回的数据对象，res.status是正常时候的状态码(必须由后端提供)，当服务器返回内容时候通过执行printText(rchatDom,服务器返回的内容)来让屏幕上显示打字机效果和服务器返回的内容，
   //模拟从服务器获取数据
    try {
      let res=await getData()
      if(res.status==200){
        //服务器获取到数据后产生打印动画
        printText(rchatDom,res.data.result.content)
      }else{
        //服务器获取错误信息
        printText(rchatDom,res.data.result.content)
      }
    } catch (error) {
      //错误提示
      printText(rchatDom,'No Data Response, Please Retry.')
    }
