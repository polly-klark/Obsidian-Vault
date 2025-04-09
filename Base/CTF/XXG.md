#forensic #writeup #PNG

> Добро пожаловать на испытание по криминалистике **XXG**! Наш следователь наткнулся на таинственный файл. Сможете ли вы раскрыть скрытое сообщение?

Изначально внутри этого файла находится PNG, но, просматривая строки, мы можем увидеть `.goutputstream`, `gimp-image-metadata` среди прочего.

Поиск приводит к некоторым результатам, но [один из них представляет особый интерес](https://www.gimp-forum.net/Thread-Data-gimp-backups-goutputstream-XXXXXX-Files), я вижу ответ с упоминанием `.xcf` файлов и смотрю, с чего `gimp-image-metadata` начать просмотр данных.

```
Gi?? ??? v014    È         ú            B  B                         õ   ????-image-grid       ¬(style solid)
(fgcolor (color-rgba 0 0 0 1))
(bgcolor (color-rgba 1 1 1 1))
(xspacing 10)
(yspacing 10)
(spacing-unit inches)
(xoffset 0)
(yoffset 0)
(offset-unit inches)
    gamma       0.45455000000000001    gimp-image-metadata      ç<?xml version='1.0' encoding='UTF-8'?>
<metadata>
  <tag name="Exif.Image.BitsPerSample">16 16 16</tag>
  <tag name="Exif.Image.ImageLength">12</tag>
  <tag name="Exif.Image.ImageWidth">200</tag>
  <tag name="Exif.Image.Orientation">1</tag>
  <tag name="Exif.Image.ResolutionUnit">2</tag>
  <tag name="Exif.Image.XResolution">300/1</tag>
  <tag name="Exif.Image.YResolution">300/1</tag>
  <tag name="Exif.Photo.ColorSpace">1</tag>
  <tag name="Xmp.tiff.Orientation">1</tag>
</metadata>
               l                   È         	????.???                 ÿ   !   ?              	          "                    
                                         
                                 %          $   ÿÿÿÿ   #   ÿÿÿÿ                       u           È            ¡      a      m           È         Ñ      M            á         ÿçãÏÿÿì¿ÿÿ]¦ÿÿã VFÂãäÿÿKÿþ]¦ÿê³EÖSS¯ÕS[V÷ãSS¯ðRPDÔÿÞOMÿã=uÿÿA¿ÿôUTÿÿã_ÿî=ã<OÿÿKÿÿôUTÿüZßã_ÿùðóÿ¬ã_ÿþÿ×=ÿqÿãlCþÿS}¿ÿ¢ Wéÿã_ÿÿ=ãqZªÿKÿÿ¢ WéÿÿIðã_ÿü¹ã_ÿý¸VôÿÅa¦?ÿãkÁáo¿ÿLðªÿã_ÿÏWãlæ%îKÿÿLðªÿvuÿã WWÎÿÿäFüã WWÎÿ 5üÿ×	¿ÿãkÙhÉ¿åOJ:ÿã VhéãkÿbNÿåOJ:ÿÿKöã_ÿúñIàÿã_ÿýýlÿë¯má>ÿãkÿGNÿ¿²ÿÿ]×ã_ÿîãkÿû+*ÿ²ÿÿ]×ÿWçã_ÿùøRÒÿÿã_ÿûþÿÿ¦ÿë®ÿáÿãkÿ~ÿ¿Dûÿÿ¼zã_ÿîãkÿÿ¾ÿDûÿÿ¼zÿeÎã_ÿù&KKã_ÿûIUZóÿüèSk^!ÿýÙVðQÿÿýkÿùBÿ ÿçãÏÿÿì¿ÿÿ]¦ÿÿã VFÂãäÿÿKÿþ]¦ÿê³E{SS¯ÕS[V÷ãSS¯ðRPDÔÿÞOMÿã=uÿÿA¿ÿôUTÿÿã_ÿî=ã<OÿÿKÿÿôUTÿüZßã_ÿùðóÿ¬ã_ÿþÿ×=ÿqÿãlCþÿS}¿ÿ¢ Wéÿã_ÿÿ=ãqZªÿKÿÿ¢ WéÿÿIðã_ÿü¹ã_ÿý¸VôÿÅa¦?ÿãkÁáo¿ÿLðªÿã_ÿÏWãlæ%îKÿÿLðªÿvuÿã WWÎÿÿäFüã WWÎÿ 5üÿ×	¿ÿãkÙhÉ¿åOJ:ÿã VhéãkÿbNÿåOJ:ÿÿKöã_ÿúñIàÿã_ÿýýlÿë¯má>ÿãkÿGNÿ¿²ÿÿ]×ã_ÿîãkÿû+*ÿ²ÿÿ]×ÿWçã_ÿùøRÒÿÿã_ÿûþÿÿ¦ÿë®ÿáÿãkÿ~ÿ¿Dûÿÿ¼zã_ÿîãkÿÿ¾ÿDûÿÿ¼zÿeÎã_ÿù&KKã_ÿûIUZóÿüèSk^!ÿýÙVðQÿÿýkÿùBÿ ÿÿ÷ÿðã XNÿãSS¯ðRPDÔÿ÷saÊÑ"Ûÿã_ÿûÇP[Pòÿöò OOáGäÿëBÿþÆÿøã_ÿÿ?ïã_ÿþÿöþEÿ÷_hÛÿã_ÿûñúÿ¢ÿþárÿû½gÿqµÿÿóÿøã_ÿüCøã_ÿý¸VôÿöuÿÿoÛÿã_ÿýþgÁÿþÐÿôAÀ>ÿÊN]ÿÿ×ùÿðã W.ÿã WWÎÿ 5üÿþûGÿûoÛÿã_ÿü>VùÿôÎE_jñÿº	·ÿ¥ÿýØDþÿøã_ÿþYÏã_ÿýýlÿþûGÿûoÛÿã_ÿþ¦ÿò®|ÿÿ4ÿÿùPÂÿÿøã_ÿÿjÀã_ÿûþÿÿ¦ÿþûGÿûoÛÿã_ÿûýÿÿºxÿû¿{ÿÿ?ÿúõ@ÿAÚÿøã WPfýã_ÿûIUZóÿþûGÿóoÛÿãOO­S\Uéÿò·X]Yìÿÿ?ÿÿ¥Z[Bÿÿýkÿù
ÿÿýkÿùÿÿýkÿùMÿ ÿÿ÷ÿðã XNÿãSS¯ðRPDÔÿ÷saÊÑ"Ûÿã_ÿûÇP[Pòÿöò OOáGäÿëBÿþÆÿøã_ÿÿ?ïã_ÿþÿöþEÿ÷_hÛÿã_ÿûñúÿ¢ÿþárÿû½gÿqµÿÿóÿøã_ÿüCøã_ÿý¸VôÿöuÿÿoÛÿã_ÿýþgÁÿþÐÿôAÀ>ÿÊN]ÿÿ×ùÿðã W.ÿã WWÎÿ 5üÿþûGÿûoÛÿã_ÿü>VùÿôÎE_jñÿº	·ÿ¥ÿýØDþÿøã_ÿþYÏã_ÿýýlÿþûGÿûoÛÿã_ÿþ¦ÿò®|ÿÿ4ÿÿùPÂÿÿøã_ÿÿjÀã_ÿûþÿÿ¦ÿþûGÿûoÛÿã_ÿûýÿÿºxÿû¿{ÿÿ?ÿúõ@ÿAÚÿøã WPfýã_ÿûIUZóÿþûGÿóoÛÿãOO­S\Uéÿò·X]Yìÿÿ?ÿÿ¥Z[Bÿÿýkÿù
ÿÿýkÿùÿÿýkÿùMÿ ÿOñDXÇP[PòãÏÿÿì¿	ÿýä
ûÿûãSS¯ÿ÷ãäÿÿKÿÑÿçÇP[PòxWGÿÿ¯ñúÿ¢ã=uÿÿA¿	ÿýfBûÿþã_ÿúã<OÿÿKÿãñúÿ¢þÿÚÿÿDüÿÿþgÁãlCþÿS}¿ÊN]ÿöÃQûÇqT§ã_ÿØãqZªÿKÿÇýeVfÿÿþgÁÿÿÿºÿÿ>VùãkÁáo¿¥ÿòýSøOûÇCþÿã WWÎÿõãlæ%îKÿÇ®ÿö>Vùÿ¸pÿRåÿò¦ãkÙhÉ¿ùPÂÿõ±ÿOûÇ}ÿÿã_ÿõãkÿbNÿÇ´ÿæ¦ÿeýßVÿÿýÿÿºxãkÿGNÿ¿ÿÿõ@ÿõTOOTÇÿÿã_ÿÖãkÿû+*ÿÇ«ÿÿýÿÿºxÿâÿu»ÿÿS\Uéãkÿ~ÿ¿¥Z[ÿõOûÇÿÿãOO­ÿëãkÿÿ¾ÿÇübU[S\Uéÿ3öSÿÿýkÿùÿÿýkÿùSÿ ÿOñDXÇP[PòãÏÿÿì¿	ÿýä
ûÿûãSS¯ÿ÷ãäÿÿKÿÑÿçÇP[PòxWGÿÿ¯ñúÿ¢ã=uÿÿA¿	ÿýfBûÿþã_ÿúã<OÿÿKÿãñúÿ¢þÿÚÿÿDüÿÿþgÁãlCþÿS}¿ÊN]ÿöÃQûÇqT§ã_ÿØãqZªÿKÿÇýeVfÿÿþgÁÿÿÿºÿÿ>VùãkÁáo¿¥ÿòýSøOûÇCþÿã WWÎÿõãlæ%îKÿÇ®ÿö>Vùÿ¸pÿRåÿò¦ãkÙhÉ¿ùPÂÿõ±ÿOûÇ}ÿÿã_ÿõãkÿbNÿÇ´ÿæ¦ÿeýßVÿÿýÿÿºxãkÿGNÿ¿ÿÿõ@ÿõTOOTÇÿÿã_ÿÖãkÿû+*ÿÇ«ÿÿýÿÿºxÿâÿu»ÿÿS\Uéãkÿ~ÿ¿¥Z[ÿõOûÇÿÿãOO­ÿëãkÿÿ¾ÿÇübU[S\Uéÿ3öSÿÿýkÿùÿÿýkÿùSÿÿûÌÄ^cÿâkÐiÿDöÿÿÜuÿRèÿÿþèÿ®-èÿÿöªÿáÿûûêÿPóÿûÄaÿGûÿþÿÿûÌÄ^cÿâkÐiÿDöÿÿÜuÿRèÿÿþèÿ®-èÿÿöªÿáÿûûêÿPóÿûÄaÿGûÿþÿ   d          2
```

Мы видим, что в начале этих данных есть несколько вопросительных знаков. Я вижу`????-image-grid`, что, как я предполагаю, `????` должно быть `gimp`.

При дальнейшем рассмотрении [.Формат XCF](https://developer.gimp.org/core/standards/xcf/#header) мы видим, что заголовок должен быть`gimp xcf`.

Тогда будут предоставлены полные данные:

```
gimp xcf v014    È         ú            B  B                         õ   gimp-image-grid       ¬(style solid)
(fgcolor (color-rgba 0 0 0 1))
(bgcolor (color-rgba 1 1 1 1))
(xspacing 10)
(yspacing 10)
(spacing-unit inches)
(xoffset 0)
(yoffset 0)
(offset-unit inches)
    gamma       0.45455000000000001    gimp-image-metadata      ç<?xml version='1.0' encoding='UTF-8'?>
<metadata>
  <tag name="Exif.Image.BitsPerSample">16 16 16</tag>
  <tag name="Exif.Image.ImageLength">12</tag>
  <tag name="Exif.Image.ImageWidth">200</tag>
  <tag name="Exif.Image.Orientation">1</tag>
  <tag name="Exif.Image.ResolutionUnit">2</tag>
  <tag name="Exif.Image.XResolution">300/1</tag>
  <tag name="Exif.Image.YResolution">300/1</tag>
  <tag name="Exif.Photo.ColorSpace">1</tag>
  <tag name="Xmp.tiff.Orientation">1</tag>
</metadata>
               l                   È         	????.???                 ÿ   !   ?              	          "                    
                                         
                                 %          $   ÿÿÿÿ   #   ÿÿÿÿ                       u           È            ¡      a      m           È         Ñ      M            á         ÿçãÏÿÿì¿ÿÿ]¦ÿÿã VFÂãäÿÿKÿþ]¦ÿê³EÖSS¯ÕS[V÷ãSS¯ðRPDÔÿÞOMÿã=uÿÿA¿ÿôUTÿÿã_ÿî=ã<OÿÿKÿÿôUTÿüZßã_ÿùðóÿ¬ã_ÿþÿ×=ÿqÿãlCþÿS}¿ÿ¢ Wéÿã_ÿÿ=ãqZªÿKÿÿ¢ WéÿÿIðã_ÿü¹ã_ÿý¸VôÿÅa¦?ÿãkÁáo¿ÿLðªÿã_ÿÏWãlæ%îKÿÿLðªÿvuÿã WWÎÿÿäFüã WWÎÿ 5üÿ×	¿ÿãkÙhÉ¿åOJ:ÿã VhéãkÿbNÿåOJ:ÿÿKöã_ÿúñIàÿã_ÿýýlÿë¯má>ÿãkÿGNÿ¿²ÿÿ]×ã_ÿîãkÿû+*ÿ²ÿÿ]×ÿWçã_ÿùøRÒÿÿã_ÿûþÿÿ¦ÿë®ÿáÿãkÿ~ÿ¿Dûÿÿ¼zã_ÿîãkÿÿ¾ÿDûÿÿ¼zÿeÎã_ÿù&KKã_ÿûIUZóÿüèSk^!ÿýÙVðQÿÿýkÿùBÿ ÿçãÏÿÿì¿ÿÿ]¦ÿÿã VFÂãäÿÿKÿþ]¦ÿê³E{SS¯ÕS[V÷ãSS¯ðRPDÔÿÞOMÿã=uÿÿA¿ÿôUTÿÿã_ÿî=ã<OÿÿKÿÿôUTÿüZßã_ÿùðóÿ¬ã_ÿþÿ×=ÿqÿãlCþÿS}¿ÿ¢ Wéÿã_ÿÿ=ãqZªÿKÿÿ¢ WéÿÿIðã_ÿü¹ã_ÿý¸VôÿÅa¦?ÿãkÁáo¿ÿLðªÿã_ÿÏWãlæ%îKÿÿLðªÿvuÿã WWÎÿÿäFüã WWÎÿ 5üÿ×	¿ÿãkÙhÉ¿åOJ:ÿã VhéãkÿbNÿåOJ:ÿÿKöã_ÿúñIàÿã_ÿýýlÿë¯má>ÿãkÿGNÿ¿²ÿÿ]×ã_ÿîãkÿû+*ÿ²ÿÿ]×ÿWçã_ÿùøRÒÿÿã_ÿûþÿÿ¦ÿë®ÿáÿãkÿ~ÿ¿Dûÿÿ¼zã_ÿîãkÿÿ¾ÿDûÿÿ¼zÿeÎã_ÿù&KKã_ÿûIUZóÿüèSk^!ÿýÙVðQÿÿýkÿùBÿ ÿÿ÷ÿðã XNÿãSS¯ðRPDÔÿ÷saÊÑ"Ûÿã_ÿûÇP[Pòÿöò OOáGäÿëBÿþÆÿøã_ÿÿ?ïã_ÿþÿöþEÿ÷_hÛÿã_ÿûñúÿ¢ÿþárÿû½gÿqµÿÿóÿøã_ÿüCøã_ÿý¸VôÿöuÿÿoÛÿã_ÿýþgÁÿþÐÿôAÀ>ÿÊN]ÿÿ×ùÿðã W.ÿã WWÎÿ 5üÿþûGÿûoÛÿã_ÿü>VùÿôÎE_jñÿº	·ÿ¥ÿýØDþÿøã_ÿþYÏã_ÿýýlÿþûGÿûoÛÿã_ÿþ¦ÿò®|ÿÿ4ÿÿùPÂÿÿøã_ÿÿjÀã_ÿûþÿÿ¦ÿþûGÿûoÛÿã_ÿûýÿÿºxÿû¿{ÿÿ?ÿúõ@ÿAÚÿøã WPfýã_ÿûIUZóÿþûGÿóoÛÿãOO­S\Uéÿò·X]Yìÿÿ?ÿÿ¥Z[Bÿÿýkÿù
ÿÿýkÿùÿÿýkÿùMÿ ÿÿ÷ÿðã XNÿãSS¯ðRPDÔÿ÷saÊÑ"Ûÿã_ÿûÇP[Pòÿöò OOáGäÿëBÿþÆÿøã_ÿÿ?ïã_ÿþÿöþEÿ÷_hÛÿã_ÿûñúÿ¢ÿþárÿû½gÿqµÿÿóÿøã_ÿüCøã_ÿý¸VôÿöuÿÿoÛÿã_ÿýþgÁÿþÐÿôAÀ>ÿÊN]ÿÿ×ùÿðã W.ÿã WWÎÿ 5üÿþûGÿûoÛÿã_ÿü>VùÿôÎE_jñÿº	·ÿ¥ÿýØDþÿøã_ÿþYÏã_ÿýýlÿþûGÿûoÛÿã_ÿþ¦ÿò®|ÿÿ4ÿÿùPÂÿÿøã_ÿÿjÀã_ÿûþÿÿ¦ÿþûGÿûoÛÿã_ÿûýÿÿºxÿû¿{ÿÿ?ÿúõ@ÿAÚÿøã WPfýã_ÿûIUZóÿþûGÿóoÛÿãOO­S\Uéÿò·X]Yìÿÿ?ÿÿ¥Z[Bÿÿýkÿù
ÿÿýkÿùÿÿýkÿùMÿ ÿOñDXÇP[PòãÏÿÿì¿	ÿýä
ûÿûãSS¯ÿ÷ãäÿÿKÿÑÿçÇP[PòxWGÿÿ¯ñúÿ¢ã=uÿÿA¿	ÿýfBûÿþã_ÿúã<OÿÿKÿãñúÿ¢þÿÚÿÿDüÿÿþgÁãlCþÿS}¿ÊN]ÿöÃQûÇqT§ã_ÿØãqZªÿKÿÇýeVfÿÿþgÁÿÿÿºÿÿ>VùãkÁáo¿¥ÿòýSøOûÇCþÿã WWÎÿõãlæ%îKÿÇ®ÿö>Vùÿ¸pÿRåÿò¦ãkÙhÉ¿ùPÂÿõ±ÿOûÇ}ÿÿã_ÿõãkÿbNÿÇ´ÿæ¦ÿeýßVÿÿýÿÿºxãkÿGNÿ¿ÿÿõ@ÿõTOOTÇÿÿã_ÿÖãkÿû+*ÿÇ«ÿÿýÿÿºxÿâÿu»ÿÿS\Uéãkÿ~ÿ¿¥Z[ÿõOûÇÿÿãOO­ÿëãkÿÿ¾ÿÇübU[S\Uéÿ3öSÿÿýkÿùÿÿýkÿùSÿ ÿOñDXÇP[PòãÏÿÿì¿	ÿýä
ûÿûãSS¯ÿ÷ãäÿÿKÿÑÿçÇP[PòxWGÿÿ¯ñúÿ¢ã=uÿÿA¿	ÿýfBûÿþã_ÿúã<OÿÿKÿãñúÿ¢þÿÚÿÿDüÿÿþgÁãlCþÿS}¿ÊN]ÿöÃQûÇqT§ã_ÿØãqZªÿKÿÇýeVfÿÿþgÁÿÿÿºÿÿ>VùãkÁáo¿¥ÿòýSøOûÇCþÿã WWÎÿõãlæ%îKÿÇ®ÿö>Vùÿ¸pÿRåÿò¦ãkÙhÉ¿ùPÂÿõ±ÿOûÇ}ÿÿã_ÿõãkÿbNÿÇ´ÿæ¦ÿeýßVÿÿýÿÿºxãkÿGNÿ¿ÿÿõ@ÿõTOOTÇÿÿã_ÿÖãkÿû+*ÿÇ«ÿÿýÿÿºxÿâÿu»ÿÿS\Uéãkÿ~ÿ¿¥Z[ÿõOûÇÿÿãOO­ÿëãkÿÿ¾ÿÇübU[S\Uéÿ3öSÿÿýkÿùÿÿýkÿùSÿÿûÌÄ^cÿâkÐiÿDöÿÿÜuÿRèÿÿþèÿ®-èÿÿöªÿáÿûûêÿPóÿûÄaÿGûÿþÿÿûÌÄ^cÿâkÐiÿDöÿÿÜuÿRèÿÿþèÿ®-èÿÿöªÿáÿûûêÿPóÿûÄaÿGûÿþÿ   d          2
```

При попытке открыть это в GIMP мы выдаем ошибку `XCF error: unsupported XCF file version 14 encountered`.

Мы можем изменить версию на `v001` в файле вверху.

И теперь мы сталкиваемся с тем, что данные повреждены, хм…

Глядя на версии файлов XCF, кажется, что [люди используют v011](https://stackoverflow.com/questions/57847730/how-can-gimp-save-older-version-of-xcf-file-format). Итак, попытка `v011` в конечном итоге дает результат!!

[![Отметить](https://seall.dev/images/ctfs/mapnactf2024/xxg.png)](https://seall.dev/images/ctfs/mapnactf2024/xxg.png)
