# AjaxFileUpload-perfect
## 网上多五花八门的AjaxFileupLoad，很难找到没有bug的版本。
此版本 修复了二次提交 无响应的bug
修改源码：
line 224
```
            $(form).submit();
            //新增
            var oldElement = jQuery('#jUploadFile' +id ,form);
            var newElement =  jQuery('#'+s.fileElementId );
            jQuery(newElement).replaceWith(oldElement);
            jQuery(oldElement).attr('id', s.fileElementId );
```


line 69
```
            // var oldElement = jQuery('#' + fileElementId);
            // var newElement = oldElement.clone(true);
            // newElement[0].files=oldElement[0].files;
            // oldElement.attr('id', fileId);
            // oldElement.before(newElement);
            // oldElement.appendTo(form);
            
            var oldElement = jQuery('#' + fileElementId);
            var newElement = oldElement.clone(true);
            newElement[0].files=oldElement[0].files;
            oldElement.attr('id', fileId);
            oldElement.before(newElement);
            oldElement.appendTo(form);

            //set attributes
            $(form).css('position', 'absolute');
            $(form).css('top', '-1200px');
            $(form).css('left', '-1200px');
            $(form).appendTo('body');
            
```

新增扩展 支持高版本JQuery
```
jQuery.extend({
    handleError: function (s, xhr, status, e) {
        if (s.error) {
            s.error.call(s.context || s, xhr, status, e);
        }
        if (s.global) {
            (s.context ? jQuery(s.context) : jQuery.event).trigger("ajaxError", [xhr, s, e]);
        }
    },
    httpData: function (xhr, type, s) {
        var ct = xhr.getResponseHeader("content-type"),
            xml = type == "xml" || !type && ct && ct.indexOf("xml") >= 0,
            data = xml ? xhr.responseXML : xhr.responseText;
        if (xml && data.documentElement.tagName == "parsererror")
            throw "parsererror";
        if (s && s.dataFilter)
            data = s.dataFilter(data, type);
        if (typeof data === "string") {
            if (type == "script")
                jQuery.globalEval(data);
            if (type == "json")
                data = window["eval"]("(" + data + ")");
        }
        return data;
    }
})
```

## 调用
```
   $.ajaxFileUpload({
            url: opt.ajax.url,
            fileElementId:opt.fileElementId,
            dataType:'json',
            secureuri: false,
            data: opt.ajax.data,
            cache:false,
            success:function (res,status) {
                        if (res.status == 1){
                                    var src = res.url;
                                    var sid = res.id;
                                    yan.fileload.dataToInput(opt, {url:src, id:sid});
                                    layer.msg('上传成功');
                        }else{
                                    layer.msg('上传失败',{icon:5});
                        }
                        layer.close(opt.load);
                        opt.ajax.succ && opt.ajax.succ(res,status);
            },
            error:function (err) {
                        layer.msg('上传失败!!',{icon:5});
                        layer.close(opt.load);
                        opt.ajax.err && opt.ajax.err(err);
            }
});
```
