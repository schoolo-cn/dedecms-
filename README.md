/* 新增
 * dedecms(织梦cms) 文章页面调用多个id下面的图集 制作轮播图 
 *  
 * 调用指定单个或者多个id 图集 格式 22,23,25 或者26  (英文逗号分隔的字符串)
 * 调用方式  
 * 详情页面用 {dede:field.字段 function="getMoreIdImg(@me)"/}  
 * 其他页面调用 [field:字段  function="getMoreIdImg(@me)"/]
 * QQ: 1830187305  
 */

function getMoreIdImg($id_str){
    //$id_str = '88,89'; //测试用
    $where_id =  str_replace('，',',',$id_str);
    global $dsql;
    $sql = "SELECT imgurls FROM `#@__addonimages`  WHERE  aid in ($where_id) order by aid DESC";
    $dsql->SetQuery($sql);
    $dsql->Execute('list');
    $result = '';
    while ($row = $dsql->GetArray("list")) {
        $imgurls = $row['imgurls'];
        if ($imgurls != '') {
            $dtp = new DedeTagParse();
            $dtp->LoadSource($imgurls);
            $images = array();
            if(is_array($dtp->CTags))
            {
                foreach($dtp->CTags as $ctag)
                {
                    if($ctag->GetName() == 'img')
                    {
                        $row1 = array();
                        $row1['width'] = $ctag->GetAtt('width');  //图片的宽度
                        $row1['height'] = $ctag->GetAtt('height'); //图片的高度
                        $row1['imgsrc'] = trim($ctag->GetInnerText());//图片
                        $row1['text'] = $ctag->GetAtt('text'); //图片的描述
                        $images[] = $row1;
                    }
                }
            }
            $dtp->Clear();
            $i = 0;
            foreach($images as $row2)
            {
                if($row2['imgsrc'] != '')
                {
                    $result .= '<li><p><img src="'.$row2['imgsrc'].'"></p><p>'.$row2['text'].'</p></li>';
                }
                $i++;
            }
        }
    }
    return $result;
}


复制放到你的文件 \include\下的extend.func.php 下面 即可  如果有问题 请联系QQ 1830187305 进行交流
