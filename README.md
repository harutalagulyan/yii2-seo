yii2-seo
========
Library for working with SEO parameters of models

Installation
composer.json
------------
```json
{
	"require":
	{
  		"demi/seo": "dev-master"
	}
}
```
Configuration
-------------

```php
return [
    'components' => [
        'view' => [
            'as seo' => [
                'class' => 'demi\seo\SeoViewBehavior',
            ]
        ],
    ],
];
```

In model file add seo model behavior:
```php
public function behaviors()
{
    $it = $this;

    return [
        'seo' => [
            'class' => \demi\seo\SeoModelBehavior::className(),
            'seoConfig' => [
                'urlField' => 'seo_url',
                'urlProduceField' => 'title',
                'titleProduceFunc' => 'title',
                'descriptionProduceFunc' => 'short_desc',
                'keysProduceFunc' => function ($model) {
                        return $model->title . ', tag1, tag2';
                    },
                'metaField' => 'seo_meta',
                'clientChange' => Yii::$app->has('user') && Yii::$app->user->can(User::ROLE_ADMIN),
                'viewRoute' => '/post/view',
                'additionalLinkParams' => function ($model) {
                        return ['category' => $model->category->seo_url];
                    },
                'languages' => 'ru',
                'controllerClassName' => 'PostController',
                'uniqueUrlFilter' => function ($query) use ($it) {
                        /* @var $query Query */
                        $query->andWhere(['category_id' => $it->category_id]);
                    },
            ],
        ],
    ];
}
```

In main layout:
```php
<head>
    <?php $this->renderMetaTags(); ?>
    ...
</head>
```

Usage
-----
In "view.php" file for model:
```php
// set SEO:meta data for current page
$this->setSeoData($model->getSeoBehavior());

// set link tag for "no index" (and optional "no follow") for current page
$this->noIndex($and_no_follow_bool);
```
or in controller:
```php
Yii::$app->view->setSeoData($model->getSeoBehavior());
Yii::$app->view->noIndex($and_no_follow_bool);
```

Get link to view
```php
// return url to material view page:
$url = $model->getViewUrl();
$url = $model->viewUrl;
// return absolute url to material view page:
$abs_url = $model->getAbsoluteViewUrl();
$abs_url = $model->absoluteViewUrl;
```

Render SEO:url and SEO:meta fields in the "_form.php" file:
```php
$model->renderFormSeoFields($active_form_or_void);
```