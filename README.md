# Objective-C

常用知识库


## 去空宏定义
      #define safeString(obj) (([obj isEqual:[NSNull null]] || (obj == nil) || [@"null" isEqual:obj] || [@"<null>" isEqual:obj] || [@"(null)" isEqual:obj]) ? @"" : ([NSString stringWithFormat:@"%@",obj]))

      #define isEmptyString(obj) (([obj isEqual:[NSNull null]] || obj==nil || [@"null" isEqual:obj]) ? 1 : 0)

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
      ###weakSelf宏用法
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
      ###strongSelf宏用法
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
      

      


      

