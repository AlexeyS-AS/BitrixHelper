# Посмотреть заказы, которые сайт отдает для 1С


```
CModule::IncludeModule('sale');
$export = new CSaleExport();

$curPage = '/bitrix/admin/1c_excha';
$arFilter = array();
$nTopCount = false;

$arFilter["UPDATED_1C"] = "N";
$arFilter["!EXTERNAL_ORDER"] = "Y";
if ($arParams["EXPORT_PAYED_ORDERS"])
    $arFilter["PAYED"] = "Y";
if ($arParams["EXPORT_ALLOW_DELIVERY_ORDERS"])
    $arFilter["ALLOW_DELIVERY"] = "Y";
if ($arParams["EXPORT_FINAL_ORDERS"] <> '') {
    $bNextExport = false;
    $arStatusToExport = [];
    $dbStatus = CSaleStatus::GetList(array("SORT" => "ASC"), array("LID" => LANGUAGE_ID));
    while ($arStatus = $dbStatus->Fetch()) {
        if ($arStatus["ID"] == $arParams["EXPORT_FINAL_ORDERS"])
            $bNextExport = true;
        if ($bNextExport)
            $arStatusToExport[] = $arStatus["ID"];
    }
    $arFilter["STATUS_ID"] = $arStatusToExport;
}
if ($arParams["SITE_LIST"])
    $arFilter["LID"] = $arParams["SITE_LIST"];

if (COption::GetOptionString("sale", "last_export_time_committed_" . $curPage, "") <> '')
    $arFilter[">=DATE_UPDATE"] = ConvertTimeStamp(COption::GetOptionString("sale", "last_export_time_committed_" . $curPage, ""), "FULL");

// С этой даты
$arFilter[">=DATE_UPDATE"] = ConvertTimeStamp(strtotime('2024-01-17 10:48:01'), "FULL");

$r = $export->export(
    array(
        'filter' => $arFilter,
        'limit' => $arParams["INTERVAL"]
    )
);
echo $r->getData()[0];
```
