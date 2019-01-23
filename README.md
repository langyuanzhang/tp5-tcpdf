# tp5-tcpdf
TP5-tcpdf 打印PDF


1.	解压tcpdf到tp5文件目录extend下（或者别的目录下，之后的代码引用文件要注意修改）
 
2.	在前端html中设置打印按钮（要用a标签以及打开新窗口_blank），如下：

```html
<a target="_blank" href="{:url('admin/apply/dyrep',['id' => $vo.id])}"><button  class="btn btn-xs  btn-warning btn-small">打印预览</button></a>
```

3.	通过url调用控制器中的dyrep方法，查询数据库得到结果$res，再渲染界面'printpdfrep.html'，得到数据$tbl，赋值到$pdf->writeHTML($tbl, true, false, false, false, '')即可。 （可自己设置打印的模式，具体可参考网站//参考网站：http://www.thinkphp.cn/code/2127.html）


```php
/**
     * 打印维修申请表
     */
    public function dyrep(){
        $id = input('param.id');
        $res = Db::name('repair')->where('id',$id)->find();

		//这里放HTML代码 
        $tbl = $this->fetch('printpdfrep',$res);
        
        // return $res;

        /////////打印部分
        require_once ROOT_PATH.'extend'.DS.'tcpdf/tcpdf.php';   
// 调用打印SDK
        
        $pdf = new \TCPDF(PDF_PAGE_ORIENTATION, PDF_UNIT, PDF_PAGE_FORMAT, true, 'UTF-8', false); 
        //参考网站：http://www.thinkphp.cn/code/2127.html 
        // 设置打印模式
        // $pdf->SetCreator(PDF_CREATOR);  
        // $pdf->SetAuthor('Nicola Asuni');  
        $pdf->SetTitle('打印预览');  
        // $pdf->SetSubject('TCPDF Tutorial');  
        // $pdf->SetKeywords('TCPDF, PDF, example, test, guide');  
        
         //删除预定义的打印 页眉/页尾
         $pdf->setPrintHeader(false);
         $pdf->setPrintFooter(false);
         // 设置页眉显示的内容 
        $pdf->SetHeaderData(PDF_HEADER_LOGO, PDF_HEADER_LOGO_WIDTH, PDF_HEADER_TITLE.' 038', PDF_HEADER_STRING);  
        // 设置页眉\页尾字体
        $pdf->setHeaderFont(Array(PDF_FONT_NAME_MAIN, '', PDF_FONT_SIZE_MAIN));  
        $pdf->setFooterFont(Array(PDF_FONT_NAME_DATA, '', PDF_FONT_SIZE_DATA));  
        // 设置默认等宽字体
        $pdf->SetDefaultMonospacedFont(PDF_FONT_MONOSPACED);  
        //设置左、上、右的间距 
        $pdf->SetMargins(PDF_MARGIN_LEFT, 0, PDF_MARGIN_RIGHT);  
        //页眉距离顶部的距离
        $pdf->SetHeaderMargin(PDF_MARGIN_HEADER); 
        //页尾距离底部的距离 
        $pdf->SetFooterMargin(PDF_MARGIN_FOOTER);  
        // 设置是否自动分页  距离底部多少距离时分页
        $pdf->SetAutoPageBreak(TRUE, PDF_MARGIN_BOTTOM);  
        // 设置图像比例因子
        $pdf->setImageScale(PDF_IMAGE_SCALE_RATIO);  
 
        // ---------------------------------------------------------  
        // 设置字体  
        $pdf->SetFont('stsongstdlight', '', 12,'', true);   
        // 增加页面
        $pdf->AddPage(); 
        //在页面输出内容 
        // $pdf->Write(0, '报表', '', 0, 'L', true, 0, false, false, 0);  
        
        //输出html
        $pdf->writeHTML($tbl, true, false, false, false, '');  
        // ---------------------------------------------------------  
        //输出PDF文档 
        $pdf->Output('report.pdf', 'I'); 

    }
```
（注意：打印乱码，关闭调试模式即可）
