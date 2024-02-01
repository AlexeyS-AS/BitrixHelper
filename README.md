# BitrixHelper

Получить ID купленных товаров(ТП) одним запросом:
```
\Bitrix\Main\Loader::includeModule("sale");

$arData = \Bitrix\Sale\Order::getList([
    'select' => ['ID', 'PRODUCT_ID'],
    'filter' => ["USER_ID" => $USER->GetID()],
    'runtime' => [
        new \Bitrix\Main\Entity\ReferenceField(
            'BASKET',
            '\Bitrix\Sale\Internals\Basket',
            ["=this.ID" => "ref.ORDER_ID"],
            ["join_type" => "left"]
        ),
        new \Bitrix\Main\Entity\ExpressionField(
            'PRODUCT_ID',
            '%s',
            ['BASKET.PRODUCT_ID']
        ),
    ],
    'cache' => ['ttl' => 60, 'cache_joins' => true],
    'order' => ['ID' => 'DESC'],
    //'limit' => 100,
])->fetchAll();

$arIds = (!empty($arData) ? array_unique(array_column($arData, 'PRODUCT_ID')) : []);
```
