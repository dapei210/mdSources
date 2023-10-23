###

GO包搜索方式：

通常标准库都在$GOROOT/src里，因此会在这里搜索；用户自定义的包或者三方包，GO是统一从$GOPATH/src里搜索；当不使用.显示表明相对导入，那么GO就会相对于$GOPATH/src导入

#### go module

