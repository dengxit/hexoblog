---
title: Laravel-Excel
date: 2020-12-31 15:45:11
tags:
---

##### 前言

>  Laravel Excel is intended at being Laravel-flavoured PhpSpreadsheet: a simple, but elegant wrapper around PhpSpreadsheet with the goal of simplifying exports and imports.

>Easily export collections to Excel.
Export queries with automatic chunking for better performance.
Queue exports for better performance.
Easily export Blade views to Excel.
Easily import to collections.
Read the Excel file in chunks.
Handle the import inserts in batches.

Laravel Excel 是一个非常契合于Laravel的表格操作工具包，它提供了丰富的操作表格文件方式，可以便捷的操作表格的导入导出

<!-- more -->

##### 项目安装

[maatwebsite/excel git 仓库](https://github.com/Maatwebsite/Laravel-Excel/)
```
composer require maatwebsite/excel
```

##### 项目文档

[文档](https://docs.laravel-excel.com/3.1/getting-started/)


##### 多sheet的大量数据文件，指定导入其中某个sheet的内容，跳过标题

├── app
│   ├── Http
│   │   ├── Controllers
│   │   │     ├── UsersController
│   ├── Imports
│   │   ├── UserFirstSheetImport.php
│   │   ├── UserImport.php

```php
class UsersImport implements WithMultipleSheets
{
    // WithConditionalSheets 选择导入的Sheet
    // WithMultipleSheets 多个Sheet表格文件
    use WithConditionalSheets;
    public function conditionalSheets(): array
    {
        return [
           'Sheet1' => new UserFirstSheetImport(),  //'Sheet1'对应sheet名称
        ];
    }
}
```

```php
class UserFirstSheetImport implements ToModel, WithChunkReading, WithBatchInserts, WithStartRow
{
    //直接使用Laravel Model 导入
    public function model(array $row)
    {
        return new User([
           'name'     => $row[0],
           'email'    => $row[1], 
           'password' => Hash::make($row[2]),
        ]);
    }
    //WithBatchInserts 分批插入
    public function batchSize(): int
    {
        return 1000;
    }
    //WithChunkReading 分批读取
    public function chunkSize(): int
    {
        return 1000;
    }
    //WithStartRow 设置起始导入行
    public function startRow(): int
    {
        return 2;
    }
}

```

```php
class UsersController
{
    public function Import()
    {
        $file = $request->file('file');
        try {
            $import = new UsersImport();
            $import->onlySheets('Sheet1');
            Excel::import($import, $file);
        } catch (\Exception $e) {
            //可以在UserFirstSheetImport处理表格业务数据验证等，报出对应的消息
            if ($e instanceof ExcelImportValidateException) {
                throw new ExcelImportException($e->getMessage());
            }
            throw new ExcelImportException('请使用给定的excel模版导入');
        }
        //导入成功后可以文件上传OSS留底或者进行其它业务处理
    }
}

```
