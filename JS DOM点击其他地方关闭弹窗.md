     show_set(){  //显示用户设置弹窗
        this.showset = true
        this.addEventListener()
        },
      addEventListener:function(){  //监听点击事件
         const self = this
         document.getElementById('').addEventListener('click',self.closesetwindow)
      },
      removeEventListeners:function(){  //移除点击事件
          const self = this;
          document.getElementById('').removeEventListener('click',self.closesetwindow);
       },
       closesetwindow:function(){  //关闭设置弹窗
         this.showset = false
         this.removeEventListeners()
       }