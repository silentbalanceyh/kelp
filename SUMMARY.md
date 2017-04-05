# Summary

## Introduction

* [Klep Introduction](README.md)

## Environment

* [1. Management Rules](environment/documentation-rules.md)
  * 1.1.Basic Rules
    * [1.1.1.Source Version](environment/documentation-rules/111source-version.md)
  * 1.2.Environment Building
    * [1.2.1.Document](environment/documentation-rules/121document.md)
* [2. Specifications](environment/specifications.md)
  * [2.1.Component Spec](environment/specifications/21component-spec.md)
    * [2.1.1.Controls](environment/specifications/211controls.md)
      * [SPC10001 - 配置子表单中DataList表格](environment/specifications/211controls/uca10001ff1a-pei-zhi-zi-biao-dan-zhong-datalist-biao-ge.md)
    * [2.1.2.Fields](environment/specifications/212fields.md)
      * [SPC20001 - 共享配置说明](environment/specifications/212fields/spc20001-shared-configuration.md)
      * [UCA20001 - field.TextBox](environment/specifications/212fields/uca10001-fieldtextbox.md)
      * [UCA20002 - field.RemoteCombo](environment/specifications/212fields/uca20002-fieldremotecombo.md)
      * [UCA20003 - field.DurationBox](environment/specifications/212fields/uca20003-fielddurationbox.md)
      * [UCA20004 - field.DatePicker](environment/specifications/212fields/uca20004-fielddatepicker.md)
      * [UCA20005 - field.PositiveBox](environment/specifications/212fields/uca20005-fieldpositivebox.md)
      * [UCA20006 - field.TabularDrop](environment/specifications/212fields/uca20006-fieldtabulardrop.md)
      * [UCA20007 - field.CurrencyBox](environment/specifications/212fields/uca20007-fieldcurrencybox.md)
      * [UCA20008 - field.ListSelector](environment/specifications/212fields/uca20008-fieldlistselector.md)
      * [UCA20009 - field.CalculateDater](environment/specifications/212fields/uca20009-fieldcalculatedater.md)
      * [UCA20010 - field.CounterBox](environment/specifications/212fields/uca20010-fieldcounterbox.md)
    * [2.1.3.Columns](environment/specifications/213columns.md)
    * [2.1.4.Op / Actions](environment/specifications/214op-actions.md)
      * [SPC40001 - 配置添加Field为List的按钮](environment/specifications/214op-actions/spc40001-list-button.md)
      * [SPC40002 - DataList中编辑/删除按钮专用配置](environment/specifications/214op-actions/spc40002-linkbar.md)
      * [UCA40001 - List字段提交弹出框验证](environment/specifications/214op-actions/uca40001-list-validation.md)
    * [2.1.5.Validation Rules](environment/specifications/215validation-rules.md)
      * [UCA50001 - Required必填验证](environment/specifications/215validation-rules/uca50001-required.md)
  * [2.2.Out-Of-Box Restful Api](environment/specifications/22out-of-box-restful-api.md)
    * 2.2.1.Security Api
      * [WS10001 - /api/oth/authorize](environment/specifications/22out-of-box-restful-api/ws10001-apiothauthorize.md)
* [3. Implementation](environment/implementation.md)
  * [3.1.Script Engine \( JS \)](environment/implementation/31script-engine.md)
  * [3.2.Restful Api](environment/implementation/32restful-configuration.md)
    * 3.2.1.Meta Environment
      * [MTS10001 - Restful配置默认值](environment/implementation/32restful-configuration/vtx10001-default-values.md)
  * [3.3.Development](environment/implementation/33development.md)
    * [DEV10001 - 不重启Engine发布脚本](environment/implementation/dev10001-bu-zhong-qi-engine-fa-bu-jiao-ben.md)
* [4. Engine Analyzing](environment/engine-analyzing.md)
  * [4.1.Structures](environment/engine-analyzing/41structures.md)

## Reference

* [1. Basic Knowledge](reference/basic-knowledge.md)
  * 1.1.Computer Language
    * [1.1.1.JavaScript](reference/basic-knowledge/11javascript-library.md)
      * [KM10001 - ECMA Script 6.0](reference/basic-knowledge/11javascript-library/111ecma-60.md)
  * 1.2.Source Control
    * [1.2.1.Git](reference/basic-knowledge/121git.md)
      * [KM10002 - Forking Workflow](reference/basic-knowledge/121git/km10002-forking-workflow.md)
  * 1.3.Cloud Computing
    * [1.3.1.Docker](reference/basic-knowledge/131docker.md)
  * 1.4.Develop Skills
    * [1.4.1.Spring Boot](reference/basic-knowledge/141spring-boot.md)
    * [1.4.2.Adom IDE](reference/basic-knowledge/142adom-ide.md)
  * [1.5.Testing](reference/basic-knowledge/15testing.md)
    * [1.5.1.Mockito](reference/basic-knowledge/15testing/151mockito.md)
    * [1.5.2.Jacoco](reference/basic-knowledge/15testing/152jacoco.md)
    * [1.5.3.Jasmine](reference/basic-knowledge/15testing/153jasmine.md)
    * [1.5.4.JUnit](reference/basic-knowledge/15testing/154junit.md)
  * [1.6.Building](reference/basic-knowledge/16building.md)
    * 1.6.1.Webpack
    * [1.6.2.Browserify](reference/basic-knowledge/162browserify.md)
    * [1.6.3.Grunt](reference/basic-knowledge/163grunt.md)
    * [1.6.4.Gulp](reference/basic-knowledge/164gulp.md)
* [2. Vert.X Works](reference/external-courses.md)
  * [2.1.Translation](reference/external-courses/21translation.md)
    * [VTX10001 - Vert.X Core](reference/external-courses/21translation/vtx10001-vertx-core.md)
    * [VTX10002 - Vert.X Web](reference/external-courses/21translation/vtx10002-vertx-web.md)
* [3. KTS Works](reference/3kts-works.md)
  * [3.1.Training](reference/3kts-works/31training.md)
  * [3.2.TS UI Component](reference/3kts-works/32ts-ui-component.md)
  * [3.3.Server \( sco-server \)](reference/3kts-works/33server-sco-server.md)
  * [3.4.Client \( sco-portal \)](reference/3kts-works/34client-sco-portal.md)
* [4. Library](reference/library.md)
  * react 15.4.2
  * react-dom 15.4.2
  * react-redux 5.0.3
  * react-router 3.0.2
  * react-router-redux 4.0.8
  * redux 3.6.0
  * redux-act 1.1.1
  * [redux-form 6.5.0](reference/library/redux-form-650.md)
  * redux-observable 0.14.1
  * redux-orm 0.9.0-rc.3
  * redux-orm-proptypes 0.1.0
  * rxjs 5.2.0
* [Exception List](reference/exception-list.md)

## Training

* KT101 - Web Service

## History

* 2012 Web Service
* 2015 Big Data
* 2016 Software Skills

## Projects

* [Htl - 零点酒店管理系统](projects/hotel-system.md)
  * 0.User Guide
  * [1.Basic Data - 基础数据](projects/hotel-system/1basic-data-ji-chu-shu-ju.md)
    * [HTL10001 - Tabular列表基础数据](projects/hotel-system/11basic-data/11tabular-data.md)
    * [HTL10002 - Number序号基础数据](projects/hotel-system/11basic-data/htl10002-numberxu-hao-ji-chu-shu-ju.md)
  * [2.Kernel Info - 核心信息](projects/hotel-system/2assist-data-fu-zhu-shu-ju.md)
    * [2.1.Restful接口配置](projects/hotel-system/21restfuljie-kou-pei-zhi.md)
      * [RST20001 - /api/htl/ticket/orders](projects/hotel-system/21restfuljie-kou-pei-zhi/rst20001-apihtlticketorders.md)
      * [RST20002 - /api/htl/ticket/inoccup](projects/hotel-system/21restfuljie-kou-pei-zhi/rst20002-apihtlticketinoccup.md)
  * [3.Complex Logical - 复杂逻辑](projects/hotel-system/2complex-logical.md)
    * [HTL30001 - 房态计算](projects/hotel-system/htl20001-fang-tai-ji-suan-shuo-ming.md)
    * [HTL30002 - 散客/团队预定](projects/hotel-system/htl30002-san-5ba2-tuan-dui-yu-ding.md)
    * [HTL30003 - 前台接待入住](projects/hotel-system/htl30003-qian-tai-jie-dai-ru-zhu.md)

## Summary

* Appendix A - 文档索引表
  * [Engine Doc Index](summary/engine-doc-index.md)
  * [Project Doc Index](summary/project-doc-index.md)

