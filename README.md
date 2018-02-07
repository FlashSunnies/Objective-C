## 去空宏定义
    #define safeString(obj) (([obj isEqual:[NSNull null]] || (obj == nil) || [@"null" isEqual:obj] || [@"<null>" isEqual:obj] || [@"(null)" isEqual:obj]) ? @"" : ([NSString stringWithFormat:@"%@",obj]))

    #define isEmptyString(obj) (([obj isEqual:[NSNull null]] || obj==nil || [@"null" isEqual:obj]) ? 1 : 0)
## 正则匹配
     #define PredicateCompare(sourceString,foundString)   [[NSPredicate predicateWithFormat:@"self MATCHES %@",sourceString] evaluateWithObject:foundString]
## 定义数据源字符串是否存在指定字符串 sourceString:数据源字符串,foundString:需要查找的字符串
     #define RANGEFOUND(sourceString,foundString)  [sourceString rangeOfString:foundString].location != NSNotFound

##  iOS_WeakSelf&&StrongSelf

### 1.普通用法
      1.weakSelf：block内部使用一次，引用计数不加1，self释放，weakSelf = nil
      
      LoginViewController *loginVC = [[LoginViewController alloc]init];
      __weak __typeof(self) weakSelf = self;
      loginVC.loginFinished = ^(){
      [weakSelf doSomeThing];//使用多次的话，就需要strongSelf
      };
      [self.navigationController pushViewController:loginVC animated:YES];
      
      2.strongSelf
      LoginViewController *loginVC = [[LoginViewController alloc]init];
      __weak __typeof(self) weakSelf = self;//block内部多次使用
      loginVC.loginFinished = ^(){
      __strong __typeof(self) strongSelf = weakSelf;
      [strongSelf doSomeThing];
      [strongSelf doOtherThing];
      };
      [self.navigationController pushViewController:loginVC animated:YES];
###  2.宏定义用法
      1.定义宏：使用前提条件，定义一下宏
      #define WeakSelf(weakSelf)      __weak __typeof(&*self)    weakSelf  = self;
      #define StrongSelf(strongSelf)  __strong __typeof(&*self) strongSelf = weakSelf;
      a. weakSelf宏用法
      /**修改用户密码*/
      - (void)requestForModyPasswordWithCode:(NSString*)messageCode mobile:(NSString*)mobile newPasswrod:(NSString*)newPassword {
      
      WeakSelf(weakSelf)
      [[TJProgressHud shareInstance]showProgressView:@"正在修改密码..."];
      [[TJNetService shareInstance] modifyPasswordWithCode:messageCode mobile:mobile passowrd:newPassword Success:^(id result) {
      /**
      .
      .
      */
      [weakSelf.navigationController popToRootViewControllerAnimated:YES];
      } Failure:^(id result) {
      [[TJProgressHud shareInstance]removeSVProgress];
      [TJToastView showWithText:@"修改密码失败，请稍候尝试" topOffset:MainScreenSize.height/2.0];
      }];
      }
      b.strongSelf宏用法
      /**发送短信验证码*/
      - (void)requestForSendMessageCodeByPhoneNumber:(NSString*)mobile{
      WeakSelf(weakSelf)
      [[TJNetService shareInstance] sendCodeOnForgetPwdWithMobile:mobile  Success:^(id result) {
      StrongSelf(strongSelf)
      [strongSelf.evbtnSendMessage setUserInteractionEnabled:NO];
      NSTimer *timer = [NSTimer scheduledTimerWithTimeInterval:1.0
      target:strongSelf
      selector:@selector(_efUpdateButtonStatus:)
      userInfo:nil repeats:YES];
      [timer fire];
      } Failure:^(id result) {
      [TJToastView showWithText:@"验证码发送失败,请稍后尝试" topOffset:140];
      }];
      }
      
      - (void)_efUpdateButtonStatus:(NSTimer*)timer{
      //do someThing...
      }
      
#cocoapods

   ## 终端报错：
      -bash: /usr/local/bin/pod: /System/Library/Frameworks/Ruby.framework/Versions/2.0/usr/bin/ruby: bad interpreter: No such file or directory
      在终端中输入：
      sudo gem install -n /usr/local/bin cocoapods
      即可解决
     ##更新所有库最简单最基本的方法
     pod update
     更新所有库一种间接直观的方法
     
     ## 打开终端
     cd ~/.cocoapods
     ls
     cd repos
     ls
     cd master
     ls
     ls -a
     git pull
     //简单的介绍一下该方法第一行cd ~/.cocoapods是进入到本地的cocoapods类库根目录 ls是显示该目录下有哪些文件之后一直cd 下去到了master文件夹
     //master中的Specs文件夹就是存放者本地的第三方库的目录,每个文件夹里有一个json文件介绍了该三方的基本情况(版本,适用iOS版本等等)
     //ls -a是显示该文件夹下的隐藏文件,会发现有一个.git文件说明该文件夹是一个git厂库,说以可以使用git的方法更新该文件夹的内容
     //git pull等待执行完毕即可
     
     


      


