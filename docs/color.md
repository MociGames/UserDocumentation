#颜色测试功能

在**墨瓷服务器**中，可使用 &+颜色符号+文字 的方式为你的发言增添色彩
在墨瓷主服务器中，如果使用了未拥有的颜色符号，则会替换成玩家设定的默认颜色
如果需要显示“&”这个符号而不被替换掉，则用“&&”。
其中，颜色符号0-9,a-f会取消前面用到的所有颜色和字体，相当于 &1info => &r&1
因此，字体符号mnol应该在颜色符号的右边，如 &f&l白色粗体
&k后面带随机字符
&r重设样式，右边的是默认颜色
在[这里](https://minecraft-zh.gamepedia.com/%E6%A0%B7%E5%BC%8F%E4%BB%A3%E7%A0%81)查看详细教程
- - - - -

<div style="text-align: center">


<input id="info" style="width: 100%" placeholder="带有颜色符号&的文字">
<button id="ConfirmBtn" type="button" onclick="onCheck()">确定</button>
<br>
<div id="fps" style="display: none;">fps</div>
<div id="result" style="background:#646464; font-size:24px"></div>
<button type="button" onclick="ontest()">性能测试</button>
</div>

<script type="text/javascript">

    function HashMap(){
        this.map = {};
    }
    HashMap.prototype = {
        put : function(k , v){// 向Map中增加元素（k, v)
            this.map[k] = v;
        },
        get : function(k){ //获取指定Key的元素值Value，失败返回Null
            if(this.map.hasOwnProperty(k)){
                return this.map[k];
            }
            return null;
        },
        remove : function(k){ // 删除指定Key的元素，成功返回True，失败返回False
            if(this.map.hasOwnProperty(k)){
                return delete this.map[k];
            }
            return false;
        },
        removeAll : function(){  //清空HashMap所有元素
            this.map = {};
        },
        containsKey : function(k){
            return this.map.hasOwnProperty(k);
        },
        keySet : function(){ //获取Map中所有KEY的数组（Array）
            var _keys = [];
            for(var i in this.map){
                _keys.push(i);
            }
            return _keys;
        }
    };
    HashMap.prototype.constructor = HashMap;

    //MCColorToHtml code by LSeng

    const color = new HashMap(16);
    const font = new HashMap(16);
    const random = new HashMap(1024);
	const small = "iIl[]`:'.,;|!"
	const LARGE = "FM?T_b^&8J$£EjD0"

    color.put('0',"000000");
    color.put('1',"00A");
    color.put('2',"0A0");
    color.put('3',"0AA");
    color.put('4',"A00");
    color.put('5',"A0A");
    color.put('6',"FA0");
    color.put('7',"AAA");
    color.put('8',"555");
    color.put('9',"55F");
    color.put('a',"5F5");
    color.put('b',"5FF");
    color.put('c',"F55");
    color.put('d',"F5F");
    color.put('e',"FF5");
    color.put('f',"fff");
    color.put('A',"5F5");
    color.put('B',"5FF");
    color.put('C',"F55");
    color.put('D',"F5F");
    color.put('E',"FF5");
    color.put('F',"000000");
    font.put('l',"font-weight: bold;");
    font.put('m',"text-decoration: line-through;");
    font.put('n',"text-decoration: underline;");
    font.put('o',"font-style: italic;");
    font.put('L',"font-weight: bold;");
    font.put('M',"text-decoration: line-through;");
    font.put('N',"text-decoration: underline;");
    font.put('O',"font-style: italic;");

    function onCheck() {
        let info_ = document.getElementById("info");
        info = info_.value;
        if(info == null){
            alert("请输入内容");
            return
        }
        document.getElementById("result").innerHTML = parse(info.replace(/&&/g,"§§").replace(/&/g,"§").replace(/§§/g,"&"));
    }

    function parse(name){
        if(name.indexOf("§") === -1){//不包含颜色符号
            return "<div style=\"display:inline; color: #AAA\">"+name+"</div>";
        }
        let result = "";

        //如果第一个字符不是颜色符号，那就剔除到第一个字符是颜色符号的部分
        let i = name.indexOf("§");
        if(i!==0){
            result += "<div style=\"display:inline; color: black\">"
                +name.substring(0,i)
                +"</div>";
            name = name.substring(i);
        }

        let lastColor = "AAA";//为保证颜色和字体都正确，设置一个最后颜色，并赋默认值0(黑色)
        let lastFont = "";//同上，不过是字体
		let lastRandom = false
        do{
            if(name.length>1){//防止最后一个字符是颜色符号
                let c = name.charAt(1);//得到颜色符号后面的字符
                if(c === 'r'){//重置
                    lastColor = "FFF";
                    lastFont = "";
					lastRandom = false;
                }else if(color.containsKey(c)){//是颜色字符(0-9，a-f)
                    lastColor = color.get(c);
                    lastFont = "";//众所周知，在字体符号后突然加一个颜色符号，字体符号会消失
					lastRandom = false;
                }else if(font.containsKey(c)) {//是字体符号(l,o,m,n)
                    //前两个if是因为两个text-decoration分开写会冲突
                    if(c==='m' && lastFont.indexOf("text-decoration: underline;") !== -1){
                        lastFont = lastFont.replace("text-decoration: underline;","text-decoration: underline line-through;");
                    }else if(c==='n' && lastFont.indexOf("text-decoration: line-through;")!==-1){
                        lastFont = lastFont.replace("text-decoration: line-through;","text-decoration: underline line-through;");
                    }else {
                        lastFont += font.get(c);//+=是为了保证多个字体符号不会被清除，如&l&m&nName
                    }
                }else if (c === 'k'){
					lastRandom = true;
				}//其他符号直接忽略
                name = name.substring(2);//清除掉前两个字符
				
				
                if(name.length===0){//颜色符号后面没东西了，停止判断
                    break;
                }

                if(name.charAt(0) === '§'){//如果清除掉前两个字符后的字符串第一个字符还是颜色符号，继续判断
                    continue;
                }else{
					if(name.indexOf("§")!== -1){//第一个字符不是颜色符号，而后面还有颜色符号需要判断
						result+="<div style=\"display:inline; color: #"
							+lastColor
							+";"
							+lastFont
							+"\">"
							+(lastRandom ? "<random>" : "")
							+name.substring(0,name.indexOf("§"))
							+(lastRandom ? "</random>" : "")
							+"</div>";
						name = name.substring(name.indexOf("§"));//同时将name变量中前面没有颜色符号的内容包含进result并从name中清除
						continue;
					}else{//颜色符号已经处理完毕，把后面的字符串处理完就可以结束判断啦
						result += "<div style=\"display:inline; color: #"
							+lastColor
							+";"
							+lastFont
							+"\">"
							+(lastRandom ? "<random>" : "")
							+name
							+(lastRandom ? "</random>" : "")
							+"</div>";
						break;
					}
                }
            }else{
                break;
            }
        } while (name.indexOf("§")!== -1);

        return result;
    }
    
	function schedule(){
		setTimeout(() => {
			var arr = document.getElementsByTagName("random");
			for(var i=0; i<arr.length; i++){
				var text = arr[i].innerText;
				if(text !== undefined){
					var result = "";
					for(var index=0; index<text.length; index++){
						var c = text.charAt(index);
						switch(c){
							case '1':
							case '\'':
							case '|':
							case ';':
							case 'l':
							case ':':
							case '.':
							case ',':
							case '!':
							case '`':
							case 'i':
							case 'I':
							case '[':
							case ']':
								result += small.charAt(Math.floor(Math.random()*small.length));
								break;
							default:
								result += LARGE.charAt(Math.floor(Math.random()*LARGE.length));
								break;
						}
					}
					arr[i].innerText = result;
				}
			}
			if(info === test){
				document.getElementById("fps").style = "display: block"
				document.getElementById("fps").innerHTML = "<p>fps: "+ fpsresult+"</p>";
			} else {
				document.getElementById("fps").style = "display: none"
			}
			schedule();
		}, 50);
	}
	schedule();
	step();
	
	var test
	
	function ontest(){
		var colors = "0123456789abcdef"
		var fonts = "mnol"
		var info = "";
		for(let i=0; i<500; i++){
			info += "&" + colors.charAt(Math.floor(Math.random()*colors.length))
			+ "&"+fonts.charAt(Math.floor(Math.random()*fonts.length)) +
			"&k"+(i % 2 == 0 ? "A" : "i")
			//info += "&1&ka&2&ki&3&ka&4&ki&5&ka&6&ka&7&ki&8&ka&9&ki&0&ka&a&ki&b&ka&c&ki&d&ka&e&ki&f"
		}
		test = info
		document.getElementById("info").value = info;
	}
	
	
		var requestAnimationFrame =  
			window.requestAnimationFrame || //Chromium  
			window.webkitRequestAnimationFrame || //Webkit 
			window.mozRequestAnimationFrame || //Mozilla Geko 
			window.oRequestAnimationFrame || //Opera Presto 
			window.msRequestAnimationFrame || //IE Trident? 
			function(callback) { //Fallback function 
			window.setTimeout(callback, 1000/60); 
			}; 
		var e,pe,pid,fps,last,offset,step; 
	 
		var fpsresult;
		fps = 0; 
		last = Date.now(); 
		function step(){ 
			offset = Date.now() - last; 
			fps += 1; 
			if( offset >= 1000 ){ 
			last += offset; 
			fpsresult = fps
			fps = 0; 
			} 
			requestAnimationFrame( step ); 
		}; 
	
	
    var info = "&00&11&22&33&44&55&66&77&88&99&aa&bb&cc&dd&ee&ff &mm删除线&f&nn下划线&f&oo斜体&f&ll粗体&fk&kk&rr重设";
    document.getElementById("result").innerHTML = parse(info.replace(/&&/g,"§§").replace(/&/g,"§").replace(/§§/g,"&"));
</script>