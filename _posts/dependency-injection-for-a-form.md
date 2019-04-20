---
title: Dependency Injection for Form and Block
date: 2019-04-20 16:11:57
tags:
	- drupal8
categories: Services and dependency injection in drupal 8
description: Using service in form and block.
---
#### Using Dependency Injection in Form
```
<?php

/**
 * @file
 * Contains \Drupal\example\Form\ExampleConfigForm.
 */
namespace Drupal\example\Form;

use Drupal\Core\Form\ConfigFormBase;
use Drupal\Core\Form\FormStateInterface;
use Drupal\Core\Config\ConfigFactoryInterface;
use Symfony\Component\DependencyInjection\ContainerInterface;
use Drupal\Core\Session\AccountProxy;

/**
 * Implements an example configuration form.
 */
class ExampleConfigForm extends ConfigFormBase {

  /**
   * Drupal\Core\Session\AccountProxy definition.
   *
   * @var AccountProxy $currentUser
   */
  protected $currentUser;

  /**
   * Class constructor.
   */
  public function __construct(ConfigFactoryInterface $config_factory, AccountProxy $current_user) {
    parent::__construct($config_factory);
    $this->currentUser = $current_user;
  }

  /**
   * {@inheritdoc}
   */
  public static function create(ContainerInterface $container) {
    return new static(
      $container->get('config.factory'),
      $container->get('current_user')
    );
  }

  /**
   * {@inheritdoc}
   */
  protected function getEditableConfigNames() {
    return [
      'example.config_form',
    ];
  }

  /**
   * {@inheritdoc}.
   */
  public function getFormId() {
    return 'example_config_form';
  }

  /**
   * {@inheritdoc}.
   */
  public function buildForm(array $form, FormStateInterface $form_state) {

    // Get current user data.
    $uid = $this->currentUser->id();
    drupal_set_message($uid);

    // ...

    $config = $this->config('example.config_form');
    return parent::buildForm($form, $form_state);
  }

  /**
   * {@inheritdoc}
   */
  public function submitForm(array &$form, FormStateInterface $form_state) {
    $this->config('example.config_form')->save();
    parent::submitForm($form, $form_state);
  }
}
```
static create() 方法返回了ExampleConfigForm的对象，在创造对象的时候，将service传入__contruct()，注意__contruct和create内的static()方法的变量必须是对应的。create()方法在Drupal\Core\DependencyInjection\ContainerInjectionInterface中声明，FormBase和ConfigFormBase都实现了这个interface。

**routing调用BuildForm的时候如何调用的create方法没找到**

#### Using Dependency in Blcok
```
<?php

namespace Drupal\drupalise\Plugin\Block;

use Drupal\Core\Block\BlockBase;
use Drupal\Core\Plugin\ContainerFactoryPluginInterface;
use Symfony\Component\DependencyInjection\ContainerInterface;
use Drupal\drupalise\Services\DrupaliseMe;

/**
 * Provides a 'Drupalise' block.
 *
 * @Block(
 *  id = "drupalise",
 *  admin_label = @Translation("Drupalise"),
 * )
 */
class Drupalise extends BlockBase implements ContainerFactoryPluginInterface{

/**
   * @var $drupalise \Drupal\drupalise\Services\DrupaliseMe
   */
  protected $drupalise;

  /**
   * @param \Symfony\Component\DependencyInjection\ContainerInterface $container
   * @param array $configuration
   * @param string $plugin_id
   * @param mixed $plugin_definition
   *
   * @return static
   */
  public static function create(ContainerInterface $container, array $configuration, $plugin_id, $plugin_definition) {
    return new static(
      $configuration,
      $plugin_id,
      $plugin_definition,
      $container->get('drupalise')
    );
  }

  /**
   * @param array $configuration
   * @param string $plugin_id
   * @param mixed $plugin_definition
   * @param \Drupal\drupalise\Services\DrupaliseMe $drupalise_me
   */
  public function __construct(array $configuration, $plugin_id, $plugin_definition, DrupaliseMe $drupalise_me) {
    parent::__construct($configuration, $plugin_id, $plugin_definition);
    $this->drupalise = $drupalise_me;
  }

  /**
   * {@inheritdoc}
   */
  public function build() {
    $slogan= $this->drupalise->Drupalise(); // Function from your service file will return 'Drop Drop Drupal !!!'
    $build = [];
    $build['drupalise']['#markup'] = 'Drupalise is best' . $slogan;

    return $build;
  }

}
```
Block如果要实现依赖注入service除了继承默认BlockBase还要实现ContainerFactoryPluginInterface
