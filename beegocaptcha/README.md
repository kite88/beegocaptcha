# beegoCaptcha接口形式返回

an example for use captcha

```
package controllers

import (
	"github.com/astaxie/beego"
	"github.com/astaxie/beego/cache"
	"github.com/astaxie/beego/utils/captcha"
)

var cpt *captcha.Captcha

func init() {
	// use beego cache system store the captcha data
	store := cache.NewMemoryCache()
	cpt = captcha.NewWithFilter("/captcha/", store)
}

type MainController struct {
	beego.Controller
}

func (this *MainController) Get() {
	this.TplName = "index.tpl"
}

func (this *MainController) Post() {
	this.TplName = "index.tpl"

	this.Data["Success"] = cpt.VerifyReq(this.Ctx.Request)
}
```

captcha.go 里增加了这个函数以接口形式返回
```
func (c *Captcha) CreateCaptchaData() (string, string) {
	value, err := c.CreateCaptcha()
	if err != nil {
	   logs.Error("Create Captcha Error:", err)
	   return "",""
	}
	return fmt.Sprintf("%s%s.png?reload=",c.URLPrefix,value) , value
}
类似这样用 建一个接口直接返回好了
func (this *MainController) GetCaptchaData() {
	data := make(map[string]string)
	srcPath,captchaId := cpt.CreateCaptchaData()
	data["captchaId"] = captchaId
	data["srcPath"] = srcPath
	this.Data["json"] = data
	this.ServeJSON()
}

	
```
