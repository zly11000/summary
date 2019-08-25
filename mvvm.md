### MVVM总结
   M-->  Model  数据模型 
   V-->  View    视图
   VM-->  ViewModel  监听model中数据的改变和试图的更新
   步骤
   通过Object.defineProperty()来劫持各个setter和getter，数据变化的时候发给订阅者，触发想要监听的回调函数
   1.对数据进行递归遍历，包括子元素，都要加上setter和getter 
   2.将模板中的变量替换成相应的数据，给每个节点绑定所对应的更新函数，然后添加监听数据的函数，当数据发生变化时，更新视图
   3.最后利用watch搭起试图与数据之间的桥梁，数据变化时试图更新。


   class Dep {  //存储监听函数
    constructor(){
        this.events = [];
    }
    addWatcher(target){
        this.events.push(target);
    }
    emitWatcher(){
        this.events.forEach(item=>{
            item.emitCk()
        })
    }
}
Dep.target = null;
const dep = new Dep();

//数据劫持
class Observer{
    constructor(data){
        if(!data || typeof data !== 'object'){
            return;
        }
        this.data = data;
        this.init();
    }
    init(){
        Object.keys(this.data).forEach(keys=>{
            this.setdata(this.data,keys,this.data[keys]);
        })
    }
    setdata(obj,keys,value){
        new Observer(value);
        Object.defineProperty(obj,keys,{
            get(){
                //添加监听数据变化
                if(Dep.target){
                    dep.addWatcher(Dep.target);
                }
                return value;
            },
            set(newValue){
                if(newValue === value) return;
                value = newValue;
                dep.emitWatcher();  //text
                new Observer(value);
            }
        })
    }
}
const utils = {
    setValue(node,attribute,data,key){
        // console.log(node,attribute,data,key)
        node[attribute] = eval('data.'+key);
    }
}



class Watcher {  //3
    constructor(data,key,ck){
        this.data = data;
        this.key = key;
        this.ck = ck;
        Dep.target = this; 
        this.getValue();
    }
    getValue(){
        let value = eval('this.data.'+this.key); //4 person preson.name
        Dep.target = null;
        return value;
    }
    emitCk(){
        this.ck(this.getValue());
    }

}


class Mvvm{
    constructor({el,data}){
        this.el = document.querySelector(el);
        this.data = data; //获取页面中的数据
        this.init();
    }
    init(){
        //nodeType元素节点
        if(this.el.nodeType !== 1){
            return;
        }
        new Observer(this.data);
        this.tempalte(this.el);
    }
    tempalte(node){
        //模板内容 {{text}} 获取属性 v-model
        if(node.nodeType === 1){ //元素节点
            let attrs = [...node.attributes];
            attrs.forEach(item=>{
                switch(item.nodeName){
                    case 'v-model':
                        //监听当前元素使用v-model="text"
                        let keys = item.nodeValue; 
                        node.addEventListener('input',()=>{
                            let val = node.value;
                            //set
                            eval('this.data.'+keys+'="'+val+'"');
                        })
                        utils.setValue(node,'value',this.data,keys)
                        //text => data.text
                    break;
                }
            })
        }else if(node.nodeType === 3){  //文本节点
            let reg = /\{\{([\w\.]+)\}\}/;
            let text = node.textContent;
            let textKeys = text.match(reg);
            if(textKeys){
                let keys = textKeys[1]; //get
                utils.setValue(node,'textContent',this.data,keys)
                //{{text}} data.text
                //添加数据监听器
                new Watcher(this.data,keys,(value)=>{  //数据变化 接受新数据
                    console.log(value);
                    node.textContent = value;
                })
            }
        }
        
        if(node.childNodes && node.childNodes.length){
            [...node.childNodes].forEach(el=>{
                this.tempalte(el);
            })
        }
    }
}
