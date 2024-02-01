# BitrixHelper

Получить ID купленных товаров(ТП) одним запросом:
[CODE]\Bitrix\Main\Loader::includeModule("sale");

$arData = \Bitrix\Sale\Order::getList(&#91;
    'select' => &#91;'ID', 'PRODUCT_ID'&#93;,
    'filter' => &#91;"USER_ID" => $USER->GetID()&#93;,
    'runtime' => &#91;
        new \Bitrix\Main\Entity\ReferenceField(
            'BASKET',
            '\Bitrix\Sale\Internals\Basket',
            &#91;"=this.ID" => "ref.ORDER_ID"&#93;,
            &#91;"join_type" => "left"&#93;
        ),
        new \Bitrix\Main\Entity\ExpressionField(
            'PRODUCT_ID',
            '%s',
            &#91;'BASKET.PRODUCT_ID'&#93;
        ),
    &#93;,
    'cache' => &#91;'ttl' => 60, 'cache_joins' => true&#93;,
    'order' => &#91;'ID' => 'DESC'&#93;,
    //'limit' => 100,
&#93;)->fetchAll();

$arIds = (!empty($arData) ? array_unique(array_column($arData, 'PRODUCT_ID')) : &#91;&#93;);
[/CODE]
