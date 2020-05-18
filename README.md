
# GatherContent REST client

[![Build Status](https://travis-ci.org/Cheppers/gathercontent-client.svg?branch=master)](https://travis-ci.org/Cheppers/gathercontent-client)
[![codecov](https://codecov.io/gh/Cheppers/gathercontent-client/branch/master/graph/badge.svg)](https://codecov.io/gh/Cheppers/gathercontent-client)

## Supported endpoints

### Legacy

Compatible with `application/vnd.gathercontent.v0.5+json`

These endpoints are essential, so we kept the support for them in this new version.
In the future these endpoints will be in the v2 API and we will replace them accordingly.

- [GET:  /me](https://docs.gathercontent.com/0.5/reference#get-me) `$gc->meGet()`
- [GET:  /accounts](https://docs.gathercontent.com/0.5/reference#get-accounts) `$gc->accountsGet()`
- [GET:  /accounts/:account_id](https://docs.gathercontent.com/0.5/reference#get-accountsaccount_id) `$gc->accountGet()`
- [GET:  /projects](https://docs.gathercontent.com/0.5/reference#get-projects) `$gc->projectsGet()`
- [GET:  /projects/:project_id](https://docs.gathercontent.com/0.5/reference#get-project-by-id) `$gc->projectGet()`
- [POST: /projects](https://docs.gathercontent.com/0.5/reference#post-projects) `$gc->projectsPost()`
- [GET:  /projects/:project_id/statuses](https://docs.gathercontent.com/0.5/reference#get-project-statuses) `$gc->projectStatusesGet()`
- [GET:  /projects/:project_id/statuses/:status_id](https://docs.gathercontent.com/0.5/reference#get-project-statuses-by-id) `$gc->projectStatusGet()`
- [POST: /items/:item_id/choose_status](https://docs.gathercontent.com/0.5/reference#post-item-choose_status) `$gc->itemChooseStatusPost()`

### Current

Compatible with `application/vnd.gathercontent.v2+json`

#### Items

- [GET:  /projects/:project_id/items](https://docs.gathercontent.com/reference#listitems) `$gc->itemsGet()`
- [GET:  /items/:item_id](https://docs.gathercontent.com/reference#getitem) `$gc->itemGet()`
- [POST: /projects/:project_id/items](https://docs.gathercontent.com/reference#createitem) `$gc->itemPost()`
- [POST: /items/:item_id/content](https://docs.gathercontent.com/reference#updateitemcontent) `$gc->itemUpdatePost()`
- [POST: /items/:item_id/rename](https://docs.gathercontent.com/reference#renameitem) `$gc->itemRenamePost()`
- [POST: /items/:item_id/move](https://docs.gathercontent.com/reference#moveitem) `$gc->itemMovePost()`
- [POST: /items/:item_id/apply_template](https://docs.gathercontent.com/reference#applytemplate) `$gc->itemApplyTemplatePost()`
- [POST: /items/:item_id/disconnect_template](https://docs.gathercontent.com/reference#disconnecttemplate) `$gc->itemDisconnectTemplatePost()`
- [POST: /items/:item_id/duplicate](https://docs.gathercontent.com/reference#duplicate) `$gc->itemDuplicatePost()`

#### Templates

- [GET:  /projects/:project_id/templates](https://docs.gathercontent.com/reference#listtemplates) `$gc->templatesGet()`
- [GET:  /templates/:template_id](https://docs.gathercontent.com/reference#gettemplate) `$gc->templateGet()`
- [POST:  /projects/:project_id/templates](https://docs.gathercontent.com/reference#createtemplate) `$gc->templatePost()`
- [DELETE:  /templates/:template_id/delete](https://docs.gathercontent.com/reference#deletetemplate) `$gc->templateDelete()`
- [POST:  /templates/:template_id/rename](https://docs.gathercontent.com/reference#renametemplate) `$gc->templateRenamePost()`
- [POST:  /templates/:template_id/duplicate](https://docs.gathercontent.com/reference#duplicatetemplate) `$gc->templateDuplicatePost()`

#### Folders

- [GET:  /folders](https://docs.gathercontent.com/reference#get-folders) `$gc->foldersGet()`


## Basic usage

To create the GatherContentClient simply pass in a Guzzle client in the constructor.

You will need:

- your e-mail address to log into GatherContent
- your [API key](https://docs.gathercontent.com/reference#authentication) from GatherContent

```php
<?php
$email = 'YOUR_GATHERCONTENT_EMAIL';
$apiKey = 'YOUR_GATHERCONTENT_API_KEY';
$client = new \GuzzleHttp\Client();
$gc = new \Cheppers\GatherContent\GatherContentClient($client);
$gc
  ->setEmail($email)
  ->setApiKey($apiKey);

try {
    $me = $gc->meGet();
}
catch (\Exception $e) {
    echo 'ERROR: ' . $e->getMessage() . PHP_EOL;
    
    exit(1);
}
echo "Email = {$me->email}" . PHP_EOL;
echo "First name = {$me->firstName}" . PHP_EOL;
echo "Last name = {$me->lastName}" . PHP_EOL;
```

The listing endpoints are returning pagination data in this new version, you can access it like this:

```php
<?php
$email = 'YOUR_GATHERCONTENT_EMAIL';
$apiKey = 'YOUR_GATHERCONTENT_API_KEY';
$client = new \GuzzleHttp\Client();
$gc = new \Cheppers\GatherContent\GatherContentClient($client);
$gc
  ->setEmail($email)
  ->setApiKey($apiKey);

try {
    $projectId = 12345;
    $items = $gc->itemsGet($projectId);
}
catch (\Exception $e) {
    echo 'ERROR: ' . $e->getMessage() . PHP_EOL;
    
    exit(1);
}

$firstItem = reset($items['data']);

echo "First content's name = {$firstItem->name}" . PHP_EOL;
echo "Pagination total = {$items['pagination']->total}" . PHP_EOL;
echo "Pagination current page = {$items['pagination']->currentPage}" . PHP_EOL;
```

The get template endpoint is returning structure object data in this new version, you can access it like this:

```php
<?php
$email = 'YOUR_GATHERCONTENT_EMAIL';
$apiKey = 'YOUR_GATHERCONTENT_API_KEY';
$client = new \GuzzleHttp\Client();
$gc = new \Cheppers\GatherContent\GatherContentClient($client);
$gc
  ->setEmail($email)
  ->setApiKey($apiKey);

try {
    $templateId = 12345;
    $template = $gc->templateGet($templateId);
}
catch (\Exception $e) {
    echo 'ERROR: ' . $e->getMessage() . PHP_EOL;
    
    exit(1);
}

echo "First content's name = {$template['data']->name}" . PHP_EOL;
echo "Structure UUID = {$template['related']->id}" . PHP_EOL;

$group = reset($template['related']->groups);

echo "Structure's first Group's name = {$group->name}" . PHP_EOL;
```

For additional parameters please visit the documentation: [/projects/:project_id/items](https://docs.gathercontent.com/reference#listitems).
