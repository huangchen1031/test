<template>
  <div class="wrapper">
    <header
      :style="{ 'background-color': primaryColor }">
      <img
        :src="cdn + 'logo.svg'"
        alt="element-logo"
        class="header-logo">
      <ul class="header-operations">
        <li @click="showThemeDialog">切换主题色</li>
      </ul>
    </header>
    <el-row class="container">
      <el-col :span="4" class="menu">
        <el-menu default-active="1">
          <el-menu-item index="1">页面1</el-menu-item>
          <el-menu-item index="2">页面2</el-menu-item>
          <el-menu-item index="3">页面3</el-menu-item>
        </el-menu>
      </el-col>
      <el-col :span="20" class="content">
        <el-breadcrumb>
          <el-breadcrumb-item>首页</el-breadcrumb-item>
          <el-breadcrumb-item>项目</el-breadcrumb-item>
          <el-breadcrumb-item>页面一</el-breadcrumb-item>
        </el-breadcrumb>
        <el-form inline :model="query" label-position="right" label-width="60px" class="query-form">
          <el-form-item label="姓名" prop="name">
            <el-input v-model="query.name" placeholder="姓名"></el-input>
          </el-form-item>
          <el-form-item label="日期" prop="date">
            <el-date-picker
                v-model="query.date"
                type="daterange"
                placeholder="日期">
            </el-date-picker>
          </el-form-item>
          <el-form-item>
            <el-button type="primary">搜索</el-button>
          </el-form-item>
        </el-form>
        <el-table :data="tableData" class="table" stripe border>
          <el-table-column prop="date" label="日期" sortable width="200"></el-table-column>
          <el-table-column prop="name" label="姓名" width="200"></el-table-column>
          <el-table-column prop="address" label="地址"></el-table-column>
          <el-table-column prop="zip" label="邮编" width="200"></el-table-column>
          <el-table-column label="操作" width="160">
            <template scope="scope">
              <el-button size="small"></el-button>
              <el-button size="small" type="danger"></el-button>
            </template>
          </el-table-column>
        </el-table>
        <el-pagination
          layout="total, sizes, prev, pager, next, jumper"
          :total="100"
          class="pagination">
        </el-pagination>
      </el-col>
    </el-row>

    <el-dialog v-model="themeDialogVisible" title="切换主题色" size="tiny">
      <el-form
        :model="colors"
        :rules="rules"
        ref="form"
        label-position="top"
        label-width="70px">
        <el-form-item label="主题色" prop="primary">
          <el-input type="color" v-model="colors.primary" class="color-input"></el-input>
        </el-form-item>
        <el-form-item class="color-buttons">
          <el-button type="primary" @click="submitForm">切换</el-button>
          <el-button @click="resetForm">重置</el-button>
        </el-form-item>
      </el-form>
    </el-dialog>
  </div>
</template>

<script>
export default {
  name: 'themePreview',
  data() {
    const colorValidator = (rule, value, callback) => {
      if (!value) {
        callback(new Error(this.langConfig.validate.required[this.lang]));
      } else if (!/^#[\dabcdef]{6}$/i.test(value)) {
        callback(new Error(this.langConfig.validate.format[this.lang]));
      } else {
        callback();
      }
    };
    return {
      cdn: `${window.location.protocol}//${window.location.host}/3.0/`,
      query: {
        name: '',
        date: '',
      },
      tableData: [],
      primaryColor: '#20a0ff',
      themeDialogVisible: false,
      colors: {
        primary: '#20a0ff',
      },
      rules: {
        primary: [
          { validator: colorValidator, trigger: 'blur' },
        ],
      },
      originalStyle: null,
    };
  },
  created() {
    this.getIndexStyle();
  },
  methods: {
    showThemeDialog() {
      this.themeDialogVisible = true;
    },
    submitForm() {},
    resetForm() {},
    // 读取文件
    getFile(url, isBlob = false) {
      return new Promise((resolve, reject) => {
        const client = new XMLHttpRequest();
        client.responseType = isBlob ? 'blob' : '';
        client.onreadystatechange = () => {
          if (client.readyState !== 4) {
            return;
          }
          if (client.status === 200) {
            const urlArr = client.responseURL.split('/');
            resolve({
              data: client.response,
              url: urlArr[urlArr.length - 1],
            });
          } else {
            reject(new Error(client.statusText));
          }
        };
        client.open('GET', url);
        client.send();
      });
    },
    // 获取主题样式文件
    getIndexStyle() {
      this.getFile(`${this.cdn}index.css`)
        .then(({ data }) => {
          this.originalStyle = this.getStyleTemplate(data);
        //   console.log(this.originalStyle);
        });
    },
    getStyleTemplate(data) {
      const colorMap = {
        '#20a0ff': 'primary',
        '#0190fe': 'secondary',
        '#fbfdff': 'darkWhite',
        '#1f2d3d': 'baseBlack',
        '#324157': 'lightBlack',
        '#48576a': 'extraLightBlack',
        '#8391a5': 'baseSilver',
        '#97a8be': 'lightSilver',
        '#bfcbd9': 'extraLightSilver',
        '#d1dbe5': 'baseGray',
        '#e4e8f1': 'lightGray',
        '#eef1f6': 'extraLightGray',
        '#1d90e6': 'buttonActive',
        '#4db3ff': 'buttonHover',
        '#dfe6ec': 'tableBorder',
        '#d2ecff': 'datepickerInRange',
        '#afddff': 'datepickerInRangeHover',
        '#1c8de0': 'selectOptionSelected',
        '#edf7ff': 'lightBackground',
      };
      Object.keys(colorMap).forEach((key) => {
        const value = colorMap[key];
        data = data.replace(new RegExp(key, 'ig'), value);
      });
      return data;
    },
  },
};
</script>

<style>
.el-breadcrumb:after,.el-breadcrumb:before,.el-form-item:after,.el-form-item:before,.el-form-item__content:after,.el-form-item__content:before {
	display: table;
	content: "";
}

.el-slider__button,.el-slider__button-wrapper {
	-webkit-user-select: none;
	-moz-user-select: none;
	-ms-user-select: none;
}

.el-alert,.el-dialog,.el-notification,.el-radio__inner,.el-switch__core {
	box-sizing: border-box;
}

.el-pagination--small .arrow.disabled,.el-table td.is-hidden>*,.el-table th.is-hidden>* {
	visibility: hidden;
}

.el-breadcrumb:after,.el-button-group:after,.el-form-item:after,.el-form-item__content:after,.el-menu:after,.el-pagination:after,.el-picker-panel__body-wrapper:after,.el-picker-panel__body:after,.el-row:after,.el-tabs__header:after {
	clear: both;
}

.el-autocomplete__suggestions.is-loading li:after {
	display: inline-block;
	height: 100%;
	content: "";
	vertical-align: middle;
}

.el-button-group:after,.el-button-group:before {
	display: table;
	content: "";
}

.el-alert__icon,.el-radio-button__inner,.el-radio__input,.el-slider__button-wrapper .el-tooltip__rel,.el-slider__runway,.el-switch,.el-table td,.el-table th {
	vertical-align: middle;
}

.el-icon-arrow-down:before {
	content: "\E600";
}

.el-icon-arrow-left:before {
	content: "\E601";
}

.el-icon-arrow-right:before {
	content: "\E602";
}

.el-icon-arrow-up:before {
	content: "\E603";
}

.el-icon-caret-bottom:before {
	content: "\E604";
}

.el-icon-caret-left:before {
	content: "\E605";
}

.el-icon-caret-right:before {
	content: "\E606";
}

.el-icon-caret-top:before {
	content: "\E607";
}

.el-icon-check:before {
	content: "\E608";
}

.el-icon-circle-check:before {
	content: "\E609";
}

.el-icon-circle-close:before {
	content: "\E60A";
}

.el-icon-circle-cross:before {
	content: "\E60B";
}

.el-icon-close:before {
	content: "\E60C";
}

.el-icon-upload:before {
	content: "\E60D";
}

.el-icon-d-arrow-left:before {
	content: "\E60E";
}

.el-icon-d-arrow-right:before {
	content: "\E60F";
}

.el-icon-d-caret:before {
	content: "\E610";
}

.el-icon-date:before {
	content: "\E611";
}

.el-icon-delete:before {
	content: "\E612";
}

.el-icon-document:before {
	content: "\E613";
}

.el-icon-edit:before {
	content: "\E614";
}

.el-icon-information:before {
	content: "\E615";
}

.el-icon-loading:before {
	content: "\E616";
}

.el-icon-menu:before {
	content: "\E617";
}

.el-icon-message:before {
	content: "\E618";
}

.el-icon-minus:before {
	content: "\E619";
}

.el-icon-more:before {
	content: "\E61A";
}

.el-icon-picture:before {
	content: "\E61B";
}

.el-icon-plus:before {
	content: "\E61C";
}

.el-icon-search:before {
	content: "\E61D";
}

.el-icon-setting:before {
	content: "\E61E";
}

.el-icon-share:before {
	content: "\E61F";
}

.el-icon-star-off:before {
	content: "\E620";
}

.el-icon-star-on:before {
	content: "\E621";
}

.el-icon-time:before {
	content: "\E622";
}

.el-icon-warning:before {
	content: "\E623";
}

.el-icon-delete2:before {
	content: "\E624";
}

.el-icon-upload2:before {
	content: "\E627";
}

.el-icon-view:before {
	content: "\E626";
}

.el-icon-loading {
	-webkit-animation: rotating 1s linear infinite;
	animation: rotating 1s linear infinite;
}

.el-icon--right {
	margin-left: 5px;
}

.el-icon--left {
	margin-right: 5px;
}

@-webkit-keyframes rotating {
	0% {
		-webkit-transform: rotate(0);
		transform: rotate(0);
	}

	to {
		-webkit-transform: rotate(1turn);
		transform: rotate(1turn);
	}
}

@keyframes rotating {
	0% {
		-webkit-transform: rotate(0);
		transform: rotate(0);
	}

	to {
		-webkit-transform: rotate(1turn);
		transform: rotate(1turn);
	}
}

.el-alert {
	position: relative;
	display: table;
	overflow: hidden;
	margin: 0;
	padding: 8px 16px;
	width: 100%;
	border-radius: 4px;
	background-color: #fff;
	color: #fff;
	opacity: 1;
	transition: opacity .2s;
}

.el-alert .el-alert__description {
	margin: 5px 0 0;
	color: #fff;
	font-size: 12px;
}

.el-alert--success {
	background-color: #13ce66;
}

.el-alert--info {
	background-color: #50bfff;
}

.el-alert--warning {
	background-color: #f7ba2a;
}

.el-alert--error {
	background-color: #ff4949;
}

.el-alert__content {
	display: table-cell;
	padding: 0 8px;
}

.el-alert__icon {
	display: table-cell;
	width: 16px;
	color: #fff;
	font-size: 16px;
}

.el-alert__icon.is-big {
	width: 28px;
	font-size: 28px;
}

.el-alert__title {
	font-size: 13px;
	line-height: 18px;
}

.el-alert__title.is-bold {
	font-weight: 700;
}

.el-alert__closebtn {
	position: absolute;
	top: 12px;
	right: 15px;
	color: #fff;
	font-size: 12px;
	opacity: 1;
	cursor: pointer;
}

.el-alert-fade-enter,.el-alert-fade-leave-active,.el-loading-fade-enter,.el-loading-fade-leave-active,.el-notification-fade-leave-active,.el-radio__original,.el-switch .label-fade-enter,.el-switch .label-fade-leave-active {
	opacity: 0;
}

.el-alert__closebtn.is-customed {
	top: 9px;
	font-style: normal;
	font-size: 13px;
}

.el-notification {
	position: fixed;
	right: 16px;
	overflow: hidden;
	padding: 20px;
	width: 330px;
	border-radius: 2px;
	background-color: #fff;
	box-shadow: 0 2px 4px rgba(0,0,0,.12),0 0 6px rgba(0,0,0,.04);
	transition: opacity .3s,right .3s,top .4s,-webkit-transform .3s;
	transition: opacity .3s,transform .3s,right .3s,top .4s;
	transition: opacity .3s,transform .3s,right .3s,top .4s,-webkit-transform .3s;
}

.el-notification .el-icon-circle-check {
	color: #13ce66;
}

.el-notification .el-icon-circle-cross {
	color: #ff4949;
}

.el-notification .el-icon-information {
	color: #50bfff;
}

.el-notification .el-icon-warning {
	color: #f7ba2a;
}

.el-notification__group {
	margin-left: 0;
}

.el-notification__group.is-with-icon {
	margin-left: 55px;
}

.el-notification__title {
	color: #1f2d3d;
	font-weight: 400;
	font-size: 16px;
}

.el-notification__content {
	margin: 10px 0 0;
	color: #8391a5;
	text-align: justify;
	font-size: 14px;
	line-height: 21px;
}

.el-notification__icon {
	position: relative;
	top: 3px;
	float: left;
	width: 40px;
	height: 40px;
	font-size: 40px;
}

.el-dialog__headerbtn,.el-pagination__rightwrapper {
	float: right;
}

.el-notification__closeBtn {
	position: absolute;
	top: 20px;
	right: 20px;
	color: #bfcbd9;
	font-size: 14px;
	cursor: pointer;
}

.el-notification__closeBtn:hover {
	color: #97a8be;
}

.el-notification-fade-enter {
	right: 0;
	-webkit-transform: translateX(100%);
	transform: translateX(100%);
}

.el-slider:after,.el-slider:before {
	display: table;
	content: "";
}

.el-slider:after {
	clear: both;
}

.el-slider__runway {
	position: relative;
	margin: 16px 0;
	width: 100%;
	height: 4px;
	border-radius: 3px;
	background-color: #e4e8f1;
	cursor: pointer;
}

.el-slider__runway.show-input {
	margin-right: 160px;
	width: auto;
}

.el-slider__runway.disabled {
	cursor: default;
}

.el-slider__runway.disabled .el-slider__bar,.el-slider__runway.disabled .el-slider__button {
	background-color: #bfcbd9;
}

.el-slider__runway.disabled .el-slider__button-wrapper.dragging,.el-slider__runway.disabled .el-slider__button-wrapper.hover,.el-slider__runway.disabled .el-slider__button-wrapper:hover {
	cursor: not-allowed;
}

.el-slider__runway.disabled .el-slider__button.dragging,.el-slider__runway.disabled .el-slider__button.hover,.el-slider__runway.disabled .el-slider__button:hover {
	cursor: not-allowed;
	-webkit-transform: scale(1);
	transform: scale(1);
}

.el-slider__input {
	float: right;
	margin-top: 3px;
}

.el-slider__bar {
	position: absolute;
	height: 4px;
	border-bottom-left-radius: 3px;
	border-top-left-radius: 3px;
	background-color: #20a0ff;
}

.el-slider__button-wrapper {
	position: absolute;
	top: -16px;
	z-index: 1001;
	width: 36px;
	height: 36px;
	background-color: transparent;
	text-align: center;
	-webkit-transform: translateX(-50%);
	transform: translateX(-50%);
	-webkit-user-select: none;
	-moz-user-select: none;
	-ms-user-select: none;
	user-select: none;
}

.el-slider__button-wrapper .el-tooltip {
	display: block;
	height: 100%;
	line-height: 1;
}

.el-slider__button-wrapper .el-tooltip:after {
	display: inline-block;
	width: 0;
	height: 100%;
	content: "";
	vertical-align: middle;
}

.el-slider__button-wrapper.hover,.el-slider__button-wrapper:hover {
	cursor: -webkit-grab;
	cursor: grab;
}

.el-slider__button-wrapper.dragging {
	cursor: -webkit-grabbing;
	cursor: grabbing;
}

.el-slider__button {
	width: 12px;
	height: 12px;
	border-radius: 50%;
	background-color: #20a0ff;
	transition: .2s;
	user-select: none;
}

.el-radio,.el-radio-button__inner,.el-slider__button,.el-switch__label {
	-webkit-user-select: none;
	-moz-user-select: none;
	-ms-user-select: none;
}

.el-slider__button.dragging,.el-slider__button.hover,.el-slider__button:hover {
	background-color: #1c8de0;
	-webkit-transform: scale(1.5);
	transform: scale(1.5);
}

.el-slider__button.hover,.el-slider__button:hover {
	cursor: -webkit-grab;
	cursor: grab;
}

.el-slider__button.dragging {
	cursor: -webkit-grabbing;
	cursor: grabbing;
}

.el-radio,.el-radio__input {
	white-space: nowrap;
	cursor: pointer;
}

.el-slider__stop {
	position: absolute;
	width: 4px;
	height: 4px;
	border-radius: 100%;
	background-color: #bfcbd9;
	-webkit-transform: translateX(-50%);
	transform: translateX(-50%);
}

.el-radio,.el-radio__inner,.el-radio__input {
	position: relative;
	display: inline-block;
}

.el-radio {
	color: #1f2d3d;
}

.el-radio+.el-radio {
	margin-left: 15px;
}

.el-radio__input {
	outline: 0;
	line-height: 1;
}

.el-radio__input.is-focus .el-radio__inner {
	border-color: #20a0ff;
}

.el-radio__input.is-checked .el-radio__inner {
	border-color: #20a0ff;
	background: #20a0ff;
}

.el-radio__input.is-checked .el-radio__inner:after {
	-webkit-transform: translate(-50%,-50%) scale(1);
	transform: translate(-50%,-50%) scale(1);
}

.el-radio__input.is-disabled .el-radio__inner {
	border-color: #d1dbe5;
	background-color: #eef1f6;
	cursor: not-allowed;
}

.el-radio__input.is-disabled .el-radio__inner:after {
	background-color: #eef1f6;
	cursor: not-allowed;
}

.el-radio__input.is-disabled .el-radio__inner+.el-radio__label {
	cursor: not-allowed;
}

.el-radio__input.is-disabled.is-checked .el-radio__inner {
	border-color: #d1dbe5;
	background-color: #d1dbe5;
}

.el-radio__inner,.el-radio__input.is-disabled.is-checked .el-radio__inner:after {
	background-color: #fff;
}

.el-radio__input.is-disabled+.el-radio__label {
	color: #bbb;
	cursor: not-allowed;
}

.el-radio__inner {
	width: 18px;
	height: 18px;
	border: 1px solid #bfcbd9;
	border-radius: 50%;
	cursor: pointer;
}

.el-radio__inner:hover {
	border-color: #20a0ff;
}

.el-radio__inner:after {
	position: absolute;
	top: 50%;
	left: 50%;
	width: 6px;
	height: 6px;
	border-radius: 50%;
	background-color: #fff;
	content: "";
	transition: -webkit-transform .15s cubic-bezier(.71,-.46,.88,.6);
	transition: transform .15s cubic-bezier(.71,-.46,.88,.6);
	transition: transform .15s cubic-bezier(.71,-.46,.88,.6),-webkit-transform .15s cubic-bezier(.71,-.46,.88,.6);
	-webkit-transform: translate(-50%,-50%) scale(0);
	transform: translate(-50%,-50%) scale(0);
}

.el-switch__core,.el-switch__label {
	width: 46px;
	height: 22px;
	cursor: pointer;
}

.el-radio__original {
	position: absolute;
	top: 0;
	right: 0;
	bottom: 0;
	left: 0;
	z-index: -1;
	margin: 0;
	outline: 0;
}

.el-radio__label {
	padding-left: 5px;
	font-size: 14px;
}

.el-radio-group {
	display: inline-block;
	font-size: 0;
	line-height: 1;
}

.el-radio-group .el-radio {
	font-size: 14px;
}

.el-radio-button {
	position: relative;
	display: inline-block;
	overflow: hidden;
}

.el-radio-button:not(:last-child) {
	margin-right: -1px;
}

.el-radio-button:first-child .el-radio-button__inner {
	border-radius: 4px 0 0 4px;
}

.el-radio-button:last-child .el-radio-button__inner {
	border-radius: 0 4px 4px 0;
}

.el-radio-button__inner {
	position: relative;
	display: inline-block;
	box-sizing: border-box;
	margin: 0;
	padding: 10px 15px;
	outline: 0;
	border: 1px solid #bfcbd9;
	border-radius: 0;
	background: #fff;
	color: #1f2d3d;
	text-align: center;
	white-space: nowrap;
	font-size: 14px;
	line-height: 1;
	cursor: pointer;
	transition: all .3s cubic-bezier(.645,.045,.355,1);
	-webkit-appearance: none;
}

.el-radio-button__inner:hover {
	color: #20a0ff;
}

.el-radio-button__inner [class*=el-icon-] {
	line-height: .9;
}

.el-radio-button__inner [class*=el-icon-]+span {
	margin-left: 5px;
}

.el-radio-button__orig-radio {
	position: absolute;
	left: -999px;
	z-index: -1;
	outline: 0;
	opacity: 0;
}

.el-radio-button__orig-radio:checked+.el-radio-button__inner {
	border-color: #20a0ff;
	background-color: #20a0ff;
	color: #fff;
}

.el-radio-button__orig-radio:disabled+.el-radio-button__inner {
	border-color: #d1dbe5;
	background-color: #eef1f6;
	background-image: none;
	color: #bfcbd9;
	cursor: not-allowed;
}

.el-radio-button--large .el-radio-button__inner {
	padding: 11px 19px;
	border-radius: 0;
	font-size: 16px;
}

.el-radio-button--small .el-radio-button__inner {
	padding: 7px 9px;
	border-radius: 0;
	font-size: 12px;
}

.el-radio-button--mini .el-radio-button__inner {
	padding: 4px;
	border-radius: 0;
	font-size: 12px;
}

.el-switch {
	position: relative;
	display: inline-block;
	height: 22px;
	font-size: 14px;
	line-height: 22px;
}

.el-switch__label,.el-switch__label * {
	position: absolute;
	display: inline-block;
	font-size: 14px;
}

.el-switch.is-disabled .el-switch__core {
	border-color: #e4e8f1!important;
	background: #e4e8f1!important;
}

.el-switch.is-disabled .el-switch__core span {
	background-color: #fbfdff!important;
}

.el-switch.is-disabled .el-switch__core~.el-switch__label * {
	color: #fbfdff!important;
}

.el-switch.is-disabled .el-switch__input:checked+.el-switch__core {
	border-color: #e4e8f1;
	background-color: #e4e8f1;
}

.el-switch.is-disabled .el-switch__core,.el-switch.is-disabled .el-switch__label {
	cursor: not-allowed;
}

.el-switch__label {
	top: 0;
	left: 0;
	z-index: 10;
	transition: .2s;
	user-select: none;
}

.el-checkbox,.el-pager,.el-switch__label {
	-webkit-user-select: none;
	-moz-user-select: none;
	-ms-user-select: none;
}

.el-switch__label * {
	top: 4px;
	color: #fff;
	line-height: 1;
}

.el-switch__label--left i {
	left: 6px;
}

.el-switch__label--right i {
	right: 6px;
}

.el-switch__input {
	display: none;
}

.el-switch__input:checked+.el-switch__core {
	border-color: #20a0ff;
	background-color: #20a0ff;
}

.el-switch__core {
	position: relative;
	display: inline-block;
	margin: 0;
	outline: 0;
	border: 1px solid #bfcbd9;
	border-radius: 12px;
	background: #bfcbd9;
	transition: border-color .3s,background-color .3s;
}

.el-switch__core .el-switch__button {
	position: absolute;
	top: 0;
	left: 0;
	z-index: 20;
	width: 16px;
	height: 16px;
	border-radius: 100%;
	background-color: #fff;
	transition: -webkit-transform .3s;
	transition: transform .3s;
	transition: transform .3s,-webkit-transform .3s;
}

.el-switch--wide .el-switch__label.el-switch__label--left span {
	left: 10px;
}

.el-switch--wide .el-switch__label.el-switch__label--right span {
	right: 10px;
}

.el-dropdown {
	position: relative;
	display: inline-block;
	color: #48576a;
	font-size: 14px;
}

.el-dropdown .el-button-group,.el-table .el-tooltip,.el-table .el-tooltip__rel {
	display: block;
}

.el-dropdown .el-dropdown__caret-button {
	padding-right: 5px;
	padding-left: 5px;
}

.el-dropdown .el-dropdown__caret-button .el-dropdown__icon {
	padding-left: 0;
}

.el-dropdown__icon {
	margin: 0 3px;
	font-size: 12px;
}

.el-dropdown-menu {
	position: absolute;
	top: 0;
	left: 0;
	z-index: 10;
	margin: 5px 0;
	padding: 6px 0;
	min-width: 100px;
	border: 1px solid #d1dbe5;
	background-color: #fff;
	box-shadow: 0 2px 4px rgba(0,0,0,.12),0 0 6px rgba(0,0,0,.12);
}

.el-dropdown-menu__item {
	margin: 0;
	padding: 0 10px;
	list-style: none;
	line-height: 36px;
	cursor: pointer;
}

.el-dropdown-menu__item:not(.is-disabled):hover {
	background-color: #e4e8f1;
	color: #48576a;
}

.el-dropdown-menu__item.is-disabled {
	color: #bfcbd9;
	cursor: default;
	pointer-events: none;
}

.el-dropdown-menu__item--divided {
	position: relative;
	margin-top: 6px;
	border-top: 1px solid #d1dbe5;
}

.el-dropdown-menu__item--divided:before {
	display: block;
	margin: 0 -10px;
	height: 6px;
	background-color: #fff;
	content: "";
}

.el-loading-mask {
	position: absolute;
	top: 0;
	right: 0;
	bottom: 0;
	left: 0;
	z-index: 10000;
	margin: 0;
	background-color: hsla(0,0%,100%,.9);
	transition: opacity .3s;
}

.el-loading-mask.is-fullscreen {
	position: fixed;
}

.el-loading-mask.is-fullscreen .el-loading-spinner {
	margin-top: -25px;
}

.el-loading-mask.is-fullscreen .el-loading-spinner .circular {
	width: 50px;
	height: 50px;
}

.el-loading-spinner {
	position: absolute;
	top: 50%;
	margin-top: -21px;
	width: 100%;
	text-align: center;
}

.el-loading-spinner .el-loading-text {
	margin: 3px 0;
	color: #20a0ff;
	font-size: 14px;
}

.el-loading-spinner .circular {
	width: 42px;
	height: 42px;
	-webkit-animation: loading-rotate 2s linear infinite;
	animation: loading-rotate 2s linear infinite;
}

.el-loading-spinner .path {
	-webkit-animation: loading-dash 1.5s ease-in-out infinite;
	animation: loading-dash 1.5s ease-in-out infinite;
	stroke-dasharray: 90,150;
	stroke-dashoffset: 0;
	stroke-width: 2;
	stroke: #20a0ff;
	stroke-linecap: round;
}

@-webkit-keyframes loading-rotate {
	to {
		-webkit-transform: rotate(1turn);
		transform: rotate(1turn);
	}
}

@keyframes loading-rotate {
	to {
		-webkit-transform: rotate(1turn);
		transform: rotate(1turn);
	}
}

.el-dialog,.el-message {
	-ms-transform: translateX(-50%);
}

@-webkit-keyframes loading-dash {
	0% {
		stroke-dasharray: 1,200;
		stroke-dashoffset: 0;
	}

	50% {
		stroke-dasharray: 90,150;
		stroke-dashoffset: -40px;
	}

	to {
		stroke-dasharray: 90,150;
		stroke-dashoffset: -120px;
	}
}

@keyframes loading-dash {
	0% {
		stroke-dasharray: 1,200;
		stroke-dashoffset: 0;
	}

	50% {
		stroke-dasharray: 90,150;
		stroke-dashoffset: -40px;
	}

	to {
		stroke-dasharray: 90,150;
		stroke-dashoffset: -120px;
	}
}

.el-dialog {
	position: absolute;
	left: 50%;
	border-radius: 2px;
	background: #fff;
	box-shadow: 0 1px 3px rgba(0,0,0,.3);
	-webkit-transform: translateX(-50%);
	transform: translateX(-50%);
}

.el-dialog--tiny {
	width: 30%;
}

.el-dialog--small {
	width: 50%;
}

.el-dialog--large {
	width: 90%;
}

.el-dialog--full {
	top: 0;
	overflow: auto;
	width: 100%;
	height: 100%;
}

.el-dialog__wrapper {
	position: fixed;
	top: 0;
	right: 0;
	bottom: 0;
	left: 0;
	overflow: auto;
	margin: 0;
}

.el-table,.el-table td,.el-table th {
	position: relative;
	box-sizing: border-box;
}

.el-dialog__header {
	padding: 20px 20px 0;
}

.el-dialog__close {
	color: #bfcbd9;
	cursor: pointer;
}

.el-dialog__close:hover {
	color: #20a0ff;
}

.el-dialog__title {
	color: #1f2d3d;
	font-weight: 700;
	font-size: 16px;
	line-height: 1;
}

.el-dialog__body {
	padding: 30px 20px;
	color: #48576a;
	font-size: 14px;
}

.el-dialog__footer {
	box-sizing: border-box;
	padding: 10px 20px 15px;
	text-align: right;
}

.dialog-fade-enter-active {
	-webkit-animation: dialog-fade-in .3s;
	animation: dialog-fade-in .3s;
}

.dialog-fade-leave-active {
	-webkit-animation: dialog-fade-out .3s;
	animation: dialog-fade-out .3s;
}

@-webkit-keyframes dialog-fade-in {
	0% {
		opacity: 0;
		-webkit-transform: translate3d(0,-20px,0);
		transform: translate3d(0,-20px,0);
	}

	to {
		opacity: 1;
		-webkit-transform: translateZ(0);
		transform: translateZ(0);
	}
}

@keyframes dialog-fade-in {
	0% {
		opacity: 0;
		-webkit-transform: translate3d(0,-20px,0);
		transform: translate3d(0,-20px,0);
	}

	to {
		opacity: 1;
		-webkit-transform: translateZ(0);
		transform: translateZ(0);
	}
}

@-webkit-keyframes dialog-fade-out {
	0% {
		opacity: 1;
		-webkit-transform: translateZ(0);
		transform: translateZ(0);
	}

	to {
		opacity: 0;
		-webkit-transform: translate3d(0,-20px,0);
		transform: translate3d(0,-20px,0);
	}
}

@keyframes dialog-fade-out {
	0% {
		opacity: 1;
		-webkit-transform: translateZ(0);
		transform: translateZ(0);
	}

	to {
		opacity: 0;
		-webkit-transform: translate3d(0,-20px,0);
		transform: translate3d(0,-20px,0);
	}
}

.el-table {
	overflow: hidden;
	width: 100%;
	max-width: 100%;
	border: 1px solid #dfe6ec;
	background-color: #fff;
	color: #1f2d3d;
	font-size: 14px;
}

.el-table .el-tooltip__rel .cell {
	white-space: nowrap;
}

.el-table td,.el-table th {
	height: 40px;
	min-width: 0;
	text-overflow: ellipsis;
}

.el-table:after,.el-table:before {
	position: absolute;
	z-index: 1;
	background-color: #dfe6ec;
	content: "";
}

.el-table td.is-right,.el-table th.is-right {
	text-align: right;
}

.el-table td.is-left,.el-table th.is-left {
	text-align: left;
}

.el-table td.is-center,.el-table th.is-center {
	text-align: center;
}

.el-table td,.el-table th.is-leaf {
	border-bottom: 1px solid #dfe6ec;
}

.el-table td.gutter,.el-table th.gutter {
	padding: 0;
	width: 15px;
	border-right-width: 0;
	border-bottom-width: 0;
}

.el-table .cell,.el-table th>div {
	box-sizing: border-box;
	padding-right: 18px;
	padding-left: 18px;
	text-overflow: ellipsis;
}

.el-table:before {
	bottom: 0;
	left: 0;
	width: 100%;
	height: 1px;
}

.el-table:after {
	top: 0;
	right: 0;
	width: 1px;
	height: 100%;
}

.el-table .caret-wrapper,.el-table th>.cell {
	position: relative;
	display: inline-block;
	vertical-align: middle;
}

.el-table th {
	background-color: #eef1f6;
	text-align: left;
}

.el-table th,.el-table th>div {
	overflow: hidden;
	white-space: nowrap;
}

.el-table th>div {
	display: inline-block;
	line-height: 40px;
}

.el-table td>div {
	box-sizing: border-box;
}

.el-table th.required>div:before {
	display: inline-block;
	margin-right: 5px;
	width: 8px;
	height: 8px;
	border-radius: 50%;
	background: #ff4d51;
	content: "";
	vertical-align: middle;
}

.el-table th>.cell {
	box-sizing: border-box;
	width: 100%;
	text-overflow: ellipsis;
	word-wrap: normal;
	line-height: 20px;
}

.el-table th>.cell.highlight {
	color: #20a0ff;
}

.el-table .caret-wrapper {
	overflow: visible;
	overflow: initial;
	margin-top: -2px;
	margin-left: 5px;
	width: 16px;
	height: 34px;
	cursor: pointer;
}

.el-table .cell,.el-table__header-wrapper {
	overflow: hidden;
}

.el-table .sort-caret {
	position: absolute;
	left: 3px;
	z-index: 2;
	display: inline-block;
	width: 0;
	height: 0;
	border: 0;
	content: "";
}

.el-table .sort-caret.ascending,.el-table .sort-caret.descending {
	border-right: 5px solid transparent;
	border-left: 5px solid transparent;
}

.el-table .sort-caret.ascending {
	top: 11px;
	border-top: none;
	border-bottom: 5px solid #97a8be;
}

.el-table .sort-caret.descending {
	bottom: 11px;
	border-top: 5px solid #97a8be;
	border-bottom: none;
}

.el-table .ascending .sort-caret.ascending {
	border-bottom-color: #48576a;
}

.el-table .descending .sort-caret.descending {
	border-top-color: #48576a;
}

.el-table td.gutter {
	width: 0;
}

.el-table .cell {
	white-space: normal;
	line-height: 24px;
	word-break: break-all;
}

.el-table tr input[type=checkbox] {
	margin: 0;
}

.el-table tr {
	background-color: #fff;
}

.el-table .hidden-columns {
	position: absolute;
	z-index: -1;
	visibility: hidden;
}

.el-table__empty-block {
	position: relative;
	width: 100%;
	height: 100%;
	min-height: 60px;
	text-align: center;
}

.el-table__empty-text {
	position: absolute;
	top: 50%;
	left: 50%;
	color: #5e7382;
	-webkit-transform: translate(-50%,-50%);
	transform: translate(-50%,-50%);
}

.el-table__expand-column .cell {
	padding: 0;
	text-align: center;
}

.el-table__expand-icon {
	position: relative;
	height: 40px;
	color: #666;
	font-size: 12px;
	cursor: pointer;
	transition: -webkit-transform .2s ease-in-out;
	transition: transform .2s ease-in-out;
	transition: transform .2s ease-in-out,-webkit-transform .2s ease-in-out;
}

.el-table__fixed-header-wrapper thead div,.el-table__header-wrapper thead div {
	background-color: #eef1f6;
	color: #1f2d3d;
}

.el-table__expand-icon>.el-icon {
	position: absolute;
	top: 50%;
	left: 50%;
	margin-top: -5px;
	margin-left: -5px;
}

.el-table__expand-icon--expanded {
	-webkit-transform: rotate(90deg);
	transform: rotate(90deg);
}

.el-table__expanded-cell {
	padding: 20px 50px;
	background-color: #fbfdff;
	box-shadow: inset 0 2px 0 #f4f4f4;
}

.el-table__expanded-cell:hover {
	background-color: #fbfdff!important;
}

.el-table--fit {
	border-right: 0;
	border-bottom: 0;
}

.el-table--border th,.el-table__fixed-right-patch {
	border-bottom: 1px solid #dfe6ec;
}

.el-table--fit td.gutter,.el-table--fit th.gutter {
	border-right-width: 1px;
}

.el-table--border td,.el-table--border th {
	border-right: 1px solid #dfe6ec;
}

.el-table__fixed,.el-table__fixed-right {
	position: absolute;
	top: 0;
	left: 0;
	overflow-x: hidden;
	box-shadow: 1px 0 8px #d3d4d6;
}

.el-table__fixed-right:before,.el-table__fixed:before {
	position: absolute;
	bottom: 0;
	left: 0;
	z-index: 4;
	width: 100%;
	height: 1px;
	background-color: #dfe6ec;
	content: "";
}

.el-table__fixed-right-patch {
	position: absolute;
	top: -1px;
	right: 0;
	background-color: #eef1f6;
}

.el-table__fixed-right {
	top: 0;
	right: 0;
	left: auto;
	box-shadow: -1px 0 8px #d3d4d6;
}

.el-table__fixed-right .el-table__fixed-body-wrapper,.el-table__fixed-right .el-table__fixed-header-wrapper {
	right: 0;
	left: auto;
}

.el-table__fixed-header-wrapper {
	position: absolute;
	top: 0;
	left: 0;
	z-index: 3;
}

.el-table__fixed-body-wrapper {
	position: absolute;
	top: 37px;
	left: 0;
	z-index: 3;
	overflow: hidden;
}

.el-table__body-wrapper,.el-table__header-wrapper {
	width: 100%;
}

.el-table__body,.el-table__header {
	table-layout: fixed;
}

.el-table__body-wrapper {
	position: relative;
	overflow: auto;
}

.el-table--striped .el-table__body tr:nth-child(2n) td {
	background: #fafafa;
}

.el-table--striped .el-table__body tr:nth-child(2n).current-row td {
	background: #edf7ff;
}

.el-table__body tr.hover-row>td {
	background-color: #eef1f6;
}

.el-table__body tr.current-row>td {
	background: #edf7ff;
}

.el-table__column-resize-proxy {
	position: absolute;
	top: 0;
	bottom: 0;
	left: 200px;
	z-index: 10;
	width: 0;
	border-left: 1px solid #dfe6ec;
}

.el-checkbox,.el-checkbox__input {
	position: relative;
	display: inline-block;
	white-space: nowrap;
	cursor: pointer;
}

.el-table__column-filter-trigger {
	display: inline-block;
	margin-left: 5px;
	line-height: 34px;
	cursor: pointer;
}

.el-table__column-filter-trigger i {
	color: #97a8be;
}

.el-table--enable-row-transition .el-table__body td {
	transition: background-color .25s ease;
}

.el-table--enable-row-hover tr:hover>td {
	background-color: #eef1f6;
}

.el-table--fluid-height .el-table__fixed,.el-table--fluid-height .el-table__fixed-right {
	bottom: 0;
	overflow: hidden;
}

.el-checkbox {
	color: #1f2d3d;
}

.el-checkbox+.el-checkbox {
	margin-left: 15px;
}

.el-checkbox__input {
	outline: 0;
	vertical-align: middle;
	line-height: 1;
}

.el-checkbox__input.is-indeterminate .el-checkbox__inner {
	border-color: #0190fe;
	background-color: #20a0ff;
}

.el-checkbox__input.is-indeterminate .el-checkbox__inner:before {
	position: absolute;
	top: 50%;
	right: 3px;
	left: 3px;
	display: block;
	margin-top: -1px;
	border: 1px solid #fff;
	content: "";
}

.el-checkbox__input.is-indeterminate .el-checkbox__inner:after {
	display: none;
}

.el-checkbox__input.is-focus .el-checkbox__inner {
	border-color: #20a0ff;
}

.el-checkbox__input.is-checked .el-checkbox__inner {
	border-color: #0190fe;
	background-color: #20a0ff;
}

.el-checkbox__input.is-checked .el-checkbox__inner:after {
	-webkit-transform: rotate(45deg) scaleY(1);
	transform: rotate(45deg) scaleY(1);
}

.el-checkbox__input.is-disabled .el-checkbox__inner {
	border-color: #d1dbe5;
	background-color: #eef1f6;
	cursor: not-allowed;
}

.el-checkbox__input.is-disabled .el-checkbox__inner:after {
	border-color: #eef1f6;
	cursor: not-allowed;
}

.el-checkbox__input.is-disabled .el-checkbox__inner+.el-checkbox__label {
	cursor: not-allowed;
}

.el-checkbox__input.is-disabled.is-checked .el-checkbox__inner {
	border-color: #d1dbe5;
	background-color: #d1dbe5;
}

.el-checkbox__input.is-disabled.is-checked .el-checkbox__inner:after {
	border-color: #fff;
}

.el-checkbox__input.is-disabled.is-indeterminate .el-checkbox__inner {
	border-color: #d1dbe5;
	background-color: #d1dbe5;
}

.el-checkbox__input.is-disabled.is-indeterminate .el-checkbox__inner:before {
	border-color: #fff;
}

.el-checkbox__input.is-disabled+.el-checkbox__label {
	color: #bbb;
	cursor: not-allowed;
}

.el-checkbox__inner {
	position: relative;
	z-index: 1;
	display: inline-block;
	box-sizing: border-box;
	width: 18px;
	height: 18px;
	border: 1px solid #bfcbd9;
	border-radius: 4px;
	background-color: #fff;
	transition: border-color .25s cubic-bezier(.71,-.46,.29,1.46),background-color .25s cubic-bezier(.71,-.46,.29,1.46);
}

.el-checkbox__inner:hover {
	border-color: #20a0ff;
}

.el-checkbox__inner:after {
	position: absolute;
	top: 1px;
	left: 5px;
	box-sizing: content-box;
	width: 4px;
	height: 8px;
	border: 2px solid #fff;
	border-top: 0;
	border-left: 0;
	content: "";
	transition: -webkit-transform .15s cubic-bezier(.71,-.46,.88,.6) .05s;
	transition: transform .15s cubic-bezier(.71,-.46,.88,.6) .05s;
	transition: transform .15s cubic-bezier(.71,-.46,.88,.6) .05s,-webkit-transform .15s cubic-bezier(.71,-.46,.88,.6) .05s;
	-webkit-transform: rotate(45deg) scaleY(0);
	transform: rotate(45deg) scaleY(0);
	-webkit-transform-origin: center;
	transform-origin: center;
}

.el-checkbox__original {
	position: absolute;
	left: -999px;
	margin: 0;
	outline: 0;
	opacity: 0;
}

.el-checkbox__label {
	padding-left: 5px;
	font-size: 14px;
}

.el-table-column--selection .cell {
	padding-right: 14px;
	padding-left: 14px;
}

.el-table-filter {
	box-sizing: border-box;
	margin: 2px 0;
	border: 1px solid #d1dbe5;
	border-radius: 2px;
	background-color: #fff;
	box-shadow: 0 2px 4px rgba(0,0,0,.12),0 0 6px rgba(0,0,0,.12);
}

.el-table-filter__list {
	margin: 0;
	padding: 5px 0;
	min-width: 100px;
	list-style: none;
}

.el-table-filter__list-item {
	padding: 0 10px;
	font-size: 14px;
	line-height: 36px;
	cursor: pointer;
}

.el-table-filter__list-item:hover {
	background-color: #e4e8f1;
	color: #48576a;
}

.el-table-filter__list-item.is-active {
	background-color: #20a0ff;
	color: #fff;
}

.el-table-filter__content {
	min-width: 100px;
}

.el-table-filter__bottom {
	padding: 8px;
	border-top: 1px solid #d1dbe5;
}

.el-table-filter__bottom button {
	padding: 0 3px;
	border: none;
	background: 0 0;
	color: #8391a5;
	font-size: 14px;
	cursor: pointer;
}

.el-table-filter__bottom button:hover {
	color: #20a0ff;
}

.el-table-filter__bottom button:focus {
	outline: 0;
}

.el-table-filter__bottom button.is-disabled {
	color: #bfcbd9;
	cursor: not-allowed;
}

.el-table-filter__checkbox-group {
	padding: 10px;
}

.el-table-filter__checkbox-group .el-checkbox {
	display: block;
	margin-bottom: 8px;
	margin-left: 5px;
}

.el-table-filter__checkbox-group .el-checkbox:last-child {
	margin-bottom: 0;
}

.el-select-dropdown {
	position: absolute;
	z-index: 1001;
	box-sizing: border-box;
	margin: 5px 0;
	border: 1px solid #d1dbe5;
	border-radius: 2px;
	background-color: #fff;
	box-shadow: 0 2px 4px rgba(0,0,0,.12),0 0 6px rgba(0,0,0,.04);
}

.el-select-dropdown .el-scrollbar.is-empty .el-select-dropdown__list {
	padding: 0;
}

.el-select-dropdown.is-multiple .el-select-dropdown__item.selected {
	background-color: #fff;
	color: #20a0ff;
}

.el-select-dropdown.is-multiple .el-select-dropdown__item.selected.hover,.el-select-dropdown__item.hover {
	background-color: #e4e8f1;
}

.el-select-dropdown.is-multiple .el-select-dropdown__item.selected:after {
	position: absolute;
	right: 10px;
	content: "\E608";
	font-size: 11px;
	font-family: element-icons;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
}

.el-select-dropdown__empty {
	margin: 0;
	padding: 10px 0;
	color: #999;
	text-align: center;
	font-size: 14px;
}

.el-select-dropdown__wrap {
	width: 100%;
	max-height: 274px;
}

.el-select-dropdown__list {
	box-sizing: border-box;
	margin: 0;
	padding: 6px 0;
	list-style: none;
}

.el-select-dropdown__item {
	position: relative;
	overflow: hidden;
	box-sizing: border-box;
	padding: 8px 10px;
	height: 36px;
	color: #48576a;
	text-overflow: ellipsis;
	white-space: nowrap;
	font-size: 14px;
	line-height: 1.5;
	cursor: pointer;
}

.el-select-dropdown__item.selected {
	background-color: #20a0ff;
	color: #fff;
}

.el-select-dropdown__item.selected.hover {
	background-color: #1c8de0;
}

.el-select-dropdown__item span {
	line-height: 1.5!important;
}

.el-select-dropdown__item.is-disabled {
	color: #bfcbd9;
	cursor: not-allowed;
}

.el-select-dropdown__item.is-disabled:hover {
	background-color: #fff;
}

.el-select-group {
	margin: 0;
	padding: 0;
}

.el-select-group .el-select-dropdown__item {
	padding-left: 20px;
}

.el-select-group__wrap {
	margin: 0;
	padding: 0;
	list-style: none;
}

.el-select-group__title {
	padding-left: 10px;
	height: 30px;
	color: #999;
	font-size: 12px;
	line-height: 30px;
}

.el-select {
	position: relative;
	display: inline-block;
}

.el-select:hover .el-input__inner {
	border-color: #8391a5;
}

.el-select .el-input__inner {
	cursor: pointer;
}

.el-select .el-input__inner:focus {
	border-color: #20a0ff;
}

.el-select .el-input .el-input__icon {
	top: 50%;
	font-size: 12px;
	line-height: 16px;
	cursor: pointer;
	transition: -webkit-transform .3s;
	transition: transform .3s;
	transition: transform .3s,-webkit-transform .3s;
}

.el-select .el-input .el-input__icon,.el-select .el-input .el-input__icon.is-show-close {
	color: #bfcbd9;
	-webkit-transform: translateY(-50%) rotate(180deg);
	transform: translateY(-50%) rotate(180deg);
}

.el-select .el-input .el-input__icon.is-show-close {
	right: 8px;
	width: 16px;
	height: 16px;
	border-radius: 100%;
	text-align: center;
	font-size: 14px;
	transition: 0s;
}

.el-select .el-input .el-input__icon.is-show-close:hover {
	color: #97a8be;
}

.el-select .el-input .el-input__icon.is-reverse {
	-webkit-transform: translateY(-50%);
	transform: translateY(-50%);
}

.el-select .el-input.is-disabled .el-input__inner {
	cursor: not-allowed;
}

.el-select .el-input.is-disabled .el-input__inner:hover {
	border-color: #d1dbe5;
}

.el-select .el-tag__close {
	margin-top: -2px;
}

.el-select .el-tag {
	box-sizing: border-box;
	margin: 3px 0 3px 6px;
	height: 24px;
	line-height: 24px;
}

.el-select__input {
	margin-left: 10px;
	padding: 0;
	height: 28px;
	border: none;
	background-color: transparent;
	color: #666;
	vertical-align: baseline;
	font-size: 14px;
	-moz-appearance: none;
	appearance: none;
}

.el-button,.el-input__inner,.el-select__input {
	outline: 0;
	-webkit-appearance: none;
}

.el-select__input.is-mini {
	height: 14px;
}

.el-select__close {
	position: absolute;
	top: 8px;
	right: 25px;
	z-index: 1000;
	color: #bfcbd9;
	font-size: 12px;
	line-height: 18px;
	cursor: pointer;
}

.el-select__close:hover {
	color: #97a8be;
}

.el-select__tags {
	position: absolute;
	top: 50%;
	z-index: 1000;
	white-space: normal;
	line-height: normal;
	-webkit-transform: translateY(-50%);
	transform: translateY(-50%);
}

.el-select__tag {
	display: inline-block;
	height: 24px;
	border-radius: 4px;
	background-color: #20a0ff;
	color: #fff;
	font-size: 14px;
	line-height: 24px;
}

.el-select__tag .el-icon-close {
	font-size: 12px;
}

.el-pagination {
	padding: 2px 5px;
	color: #48576a;
	white-space: nowrap;
}

.el-pagination:after,.el-pagination:before {
	display: table;
	content: "";
}

.el-pagination button,.el-pagination span {
	display: inline-block;
	box-sizing: border-box;
	height: 28px;
	min-width: 28px;
	vertical-align: top;
	font-size: 13px;
	line-height: 28px;
}

.el-pagination .el-select .el-input {
	width: 110px;
}

.el-pagination .el-select .el-input input {
	padding-right: 25px;
	height: 28px;
	border-radius: 2px;
}

.el-pagination button {
	padding: 0 6px;
	border: none;
	background: 0 0;
}

.el-pagination button:focus {
	outline: 0;
}

.el-pagination button:hover {
	color: #20a0ff;
}

.el-pagination button.disabled {
	background-color: #fff;
	color: #e4e4e4;
	cursor: not-allowed;
}

.el-pager li,.el-pager li.btn-quicknext:hover,.el-pager li.btn-quickprev:hover {
	cursor: pointer;
}

.el-pagination .btn-next,.el-pagination .btn-prev {
	margin: 0;
	border: 1px solid #d1dbe5;
	background: 50% no-repeat #fff;
	background-size: 16px;
	color: #97a8be;
	cursor: pointer;
}

.el-pagination .btn-next .el-icon,.el-pagination .btn-prev .el-icon {
	display: block;
	font-size: 12px;
}

.el-pagination .btn-prev {
	border-right: 0;
	border-radius: 2px 0 0 2px;
}

.el-pagination .btn-next {
	border-left: 0;
	border-radius: 0 2px 2px 0;
}

.el-pagination--small .btn-next,.el-pagination--small .btn-prev,.el-pagination--small .el-pager li,.el-pagination--small .el-pager li:last-child {
	height: 22px;
	min-width: 22px;
	border-color: transparent;
	font-size: 12px;
	line-height: 22px;
}

.el-pagination--small .el-pager li {
	border-radius: 2px;
}

.el-pagination__sizes {
	margin: 0 10px 0 0;
}

.el-pagination__sizes .el-input .el-input__inner {
	border-color: #d1dbe5;
	font-size: 13px;
}

.el-pagination__sizes .el-input .el-input__inner:hover {
	border-color: #20a0ff;
}

.el-pagination__jump {
	margin-left: 10px;
}

.el-pagination__total {
	margin: 0 10px;
}

.el-pagination__editor {
	box-sizing: border-box;
	margin: 0 6px;
	padding: 4px 2px;
	width: 30px;
	border: 1px solid #d1dbe5;
	border-radius: 2px;
	text-align: center;
	line-height: 18px;
	transition: border .3s;
}

.el-pager,.el-pager li {
	display: inline-block;
	margin: 0;
	vertical-align: top;
}

.el-pagination__editor::-webkit-inner-spin-button,.el-pagination__editor::-webkit-outer-spin-button {
	margin: 0;
	-webkit-appearance: none;
}

.el-pagination__editor:focus {
	outline: 0;
	border-color: #20a0ff;
}

.el-pager {
	padding: 0;
	list-style: none;
	font-size: 0;
	-webkit-user-select: none;
	user-select: none;
}

.el-button,.el-date-table,.el-pager {
	-moz-user-select: none;
	-ms-user-select: none;
}

.el-button,.el-date-table,.el-message-box {
	-webkit-user-select: none;
}

.el-pager li {
	box-sizing: border-box;
	padding: 0 4px;
	height: 28px;
	min-width: 28px;
	border: 1px solid #d1dbe5;
	border-right: 0;
	background: #fff;
	text-align: center;
	font-size: 13px;
	line-height: 28px;
}

.el-popover[x-placement^=bottom],.el-tooltip__popper[x-placement^=bottom] {
	margin-top: 12px;
}

.el-pager li:last-child {
	border-right: 1px solid #d1dbe5;
}

.el-pager li.btn-quicknext,.el-pager li.btn-quickprev {
	color: #97a8be;
	line-height: 28px;
}

.el-pager li.active+li {
	padding-left: 5px;
	border-left: 0;
}

.el-pager li:hover {
	color: #20a0ff;
}

.el-pager li.active {
	border-color: #20a0ff;
	background-color: #20a0ff;
	color: #fff;
	cursor: default;
}

.el-popover {
	position: absolute;
	z-index: 2000;
	padding: 10px;
	min-width: 150px;
	border: 1px solid #d1dbe5;
	border-radius: 2px;
	background: #fff;
	box-shadow: 0 2px 4px 0 rgba(0,0,0,.12),0 0 6px 0 rgba(0,0,0,.04);
	font-size: 12px;
}

.el-popover .popper__arrow,.el-popover .popper__arrow:after {
	position: absolute;
	display: block;
	width: 0;
	height: 0;
	border-color: transparent;
	border-style: solid;
}

.el-tooltip,.el-tooltip__rel {
	display: inline-block;
}

.el-popover .popper__arrow {
	border-width: 6px;
}

.el-popover .popper__arrow:after {
	border-width: 6px;
	content: " ";
}

.el-popover[x-placement^=top] {
	margin-bottom: 12px;
}

.el-popover[x-placement^=top] .popper__arrow {
	bottom: -6px;
	left: 50%;
	margin-right: 3px;
	border-top-color: #d1dbe5;
	border-bottom-width: 0;
}

.el-popover[x-placement^=top] .popper__arrow:after {
	bottom: 1px;
	margin-left: -6px;
	border-top-color: #fff;
	border-bottom-width: 0;
}

.el-popover[x-placement^=bottom] .popper__arrow {
	top: -6px;
	left: 50%;
	margin-right: 3px;
	border-top-width: 0;
	border-bottom-color: #d1dbe5;
}

.el-popover[x-placement^=left],.el-tooltip__popper[x-placement^=left] {
	margin-right: 12px;
}

.el-popover[x-placement^=bottom] .popper__arrow:after {
	top: 1px;
	margin-left: -6px;
	border-top-width: 0;
	border-bottom-color: #fff;
}

.el-popover[x-placement^=right] {
	margin-left: 12px;
}

.el-popover[x-placement^=right] .popper__arrow {
	top: 50%;
	left: -6px;
	margin-bottom: 3px;
	border-right-color: #d1dbe5;
	border-left-width: 0;
}

.el-popover[x-placement^=right] .popper__arrow:after {
	bottom: -6px;
	left: 1px;
	border-right-color: #fff;
	border-left-width: 0;
}

.el-popover[x-placement^=left] .popper__arrow {
	top: 50%;
	right: -6px;
	margin-bottom: 3px;
	border-right-width: 0;
	border-left-color: #d1dbe5;
}

.el-popover[x-placement^=left] .popper__arrow:after {
	right: 1px;
	bottom: -6px;
	margin-left: -6px;
	border-right-width: 0;
	border-left-color: #fff;
}

.el-popover__title {
	margin-bottom: 9px;
	color: #1f2d3d;
	font-size: 13px;
	line-height: 1;
}

.el-tooltip__rel {
	position: relative;
}

.el-tooltip__popper {
	position: absolute;
	z-index: 2000;
	padding: 10px;
	border-radius: 4px;
	font-size: 12px;
	line-height: 1.2;
}

.el-tooltip__popper .popper__arrow,.el-tooltip__popper .popper__arrow:after {
	position: absolute;
	display: block;
	width: 0;
	height: 0;
	border-color: transparent;
	border-style: solid;
}

.el-autocomplete,.el-button,.el-button-group,.el-message-box,.el-message-box__close,.el-message__group p {
	display: inline-block;
}

.el-tooltip__popper .popper__arrow {
	border-width: 6px;
}

.el-tooltip__popper .popper__arrow:after {
	border-width: 5px;
	content: " ";
}

.el-tooltip__popper[x-placement^=top] {
	margin-bottom: 12px;
}

.el-tooltip__popper[x-placement^=top] .popper__arrow {
	bottom: -6px;
	border-top-color: #1f2d3d;
	border-bottom-width: 0;
}

.el-tooltip__popper[x-placement^=top] .popper__arrow:after {
	bottom: 1px;
	margin-left: -5px;
	border-top-color: #1f2d3d;
	border-bottom-width: 0;
}

.el-tooltip__popper[x-placement^=bottom] .popper__arrow {
	top: -6px;
	border-top-width: 0;
	border-bottom-color: #1f2d3d;
}

.el-tooltip__popper[x-placement^=bottom] .popper__arrow:after {
	top: 1px;
	margin-left: -5px;
	border-top-width: 0;
	border-bottom-color: #1f2d3d;
}

.el-tooltip__popper[x-placement^=right] {
	margin-left: 12px;
}

.el-tooltip__popper[x-placement^=right] .popper__arrow {
	left: -6px;
	border-right-color: #1f2d3d;
	border-left-width: 0;
}

.el-tooltip__popper[x-placement^=right] .popper__arrow:after {
	bottom: -5px;
	left: 1px;
	border-right-color: #1f2d3d;
	border-left-width: 0;
}

.el-tooltip__popper[x-placement^=left] .popper__arrow {
	right: -6px;
	border-right-width: 0;
	border-left-color: #1f2d3d;
}

.el-tooltip__popper[x-placement^=left] .popper__arrow:after {
	right: 1px;
	bottom: -5px;
	margin-left: -5px;
	border-right-width: 0;
	border-left-color: #1f2d3d;
}

.el-tooltip__popper.is-light {
	border: 1px solid #1f2d3d;
	background: #fff;
}

.el-tooltip__popper.is-light[x-placement^=top] .popper__arrow {
	border-top-color: #1f2d3d;
}

.el-tooltip__popper.is-light[x-placement^=top] .popper__arrow:after {
	border-top-color: #fff;
}

.el-tooltip__popper.is-light[x-placement^=bottom] .popper__arrow {
	border-bottom-color: #1f2d3d;
}

.el-tooltip__popper.is-light[x-placement^=bottom] .popper__arrow:after {
	border-bottom-color: #fff;
}

.el-tooltip__popper.is-light[x-placement^=left] .popper__arrow {
	border-left-color: #1f2d3d;
}

.el-tooltip__popper.is-light[x-placement^=left] .popper__arrow:after {
	border-left-color: #fff;
}

.el-tooltip__popper.is-light[x-placement^=right] .popper__arrow {
	border-right-color: #1f2d3d;
}

.el-tooltip__popper.is-light[x-placement^=right] .popper__arrow:after {
	border-right-color: #fff;
}

.el-tooltip__popper.is-dark {
	background: #1f2d3d;
	color: #fff;
}

.el-autocomplete {
	position: relative;
}

.el-autocomplete__suggestions {
	position: absolute;
	top: 110%;
	left: 0;
	z-index: 10;
	overflow: auto;
	box-sizing: border-box;
	margin: 5px 0 0;
	padding: 6px 0;
	width: 100%;
	max-height: 280px;
	border: 1px solid #d1dbe5;
	border-radius: 2px;
	background-color: #fff;
	box-shadow: 0 0 6px 0 rgba(0,0,0,.04),0 2px 4px 0 rgba(0,0,0,.12);
}

.el-message,.el-time-panel {
	box-shadow: 0 2px 4px rgba(0,0,0,.12),0 0 6px rgba(0,0,0,.04);
}

.el-autocomplete__suggestions li {
	overflow: hidden;
	margin: 0;
	padding: 0 10px;
	color: #48576a;
	list-style: none;
	text-overflow: ellipsis;
	white-space: nowrap;
	font-size: 14px;
	line-height: 36px;
	cursor: pointer;
}

.el-autocomplete__suggestions li:hover {
	background-color: #e4e8f1;
}

.el-autocomplete__suggestions li.highlighted {
	background-color: #20a0ff;
	color: #fff;
}

.el-autocomplete__suggestions li:active {
	background-color: #0082e6;
}

.el-autocomplete__suggestions.is-loading li:hover,.el-message {
	background-color: #fff;
}

.el-autocomplete__suggestions li.divider {
	margin-top: 6px;
	border-top: 1px solid #d1dbe5;
}

.el-autocomplete__suggestions li.divider:last-child {
	margin-bottom: -6px;
}

.el-autocomplete__suggestions.is-loading li {
	height: 100px;
	color: #999;
	text-align: center;
	font-size: 20px;
	line-height: 100px;
}

.el-autocomplete__suggestions.is-loading .el-icon-loading {
	vertical-align: middle;
}

.el-message {
	position: fixed;
	top: 20px;
	left: 50%;
	overflow: hidden;
	box-sizing: border-box;
	padding: 10px 12px;
	min-width: 300px;
	border-radius: 2px;
	transition: opacity .3s,-webkit-transform .4s;
	transition: opacity .3s,transform .4s;
	transition: opacity .3s,transform .4s,-webkit-transform .4s;
	-webkit-transform: translateX(-50%);
	transform: translateX(-50%);
}

.el-message .el-icon-circle-check {
	color: #13ce66;
}

.el-message .el-icon-circle-cross {
	color: #ff4949;
}

.el-message .el-icon-information {
	color: #50bfff;
}

.el-message .el-icon-warning {
	color: #f7ba2a;
}

.el-message__group {
	position: relative;
	margin-left: 38px;
	height: 20px;
}

.el-message__group p {
	margin: 0 34px 0 0;
	color: #8391a5;
	vertical-align: middle;
	text-align: justify;
	white-space: nowrap;
	font-size: 14px;
	line-height: 20px;
}

.el-message__group.is-with-icon {
	margin-left: 0;
}

.el-message__img {
	position: absolute;
	top: 0;
	left: 0;
	width: 40px;
	height: 40px;
}

.el-message__icon {
	margin-right: 8px;
	vertical-align: middle;
}

.el-message__closeBtn {
	position: absolute;
	top: 3px;
	right: 0;
	color: #bfcbd9;
	font-size: 14px;
	cursor: pointer;
}

.el-message__closeBtn:hover {
	color: #97a8be;
}

.el-message-fade-enter,.el-message-fade-leave-active {
	opacity: 0;
	-webkit-transform: translate(-50%,-100%);
	transform: translate(-50%,-100%);
}

.v-modal-enter {
	-webkit-animation: v-modal-in .2s ease;
	animation: v-modal-in .2s ease;
}

.v-modal-leave {
	-webkit-animation: v-modal-out .2s ease forwards;
	animation: v-modal-out .2s ease forwards;
}

@-webkit-keyframes v-modal-in {
	0% {
		opacity: 0;
	}
}

@keyframes v-modal-in {
	0% {
		opacity: 0;
	}
}

@-webkit-keyframes v-modal-out {
	to {
		opacity: 0;
	}
}

@keyframes v-modal-out {
	to {
		opacity: 0;
	}
}

.v-modal {
	position: fixed;
	top: 0;
	left: 0;
	width: 100%;
	height: 100%;
	background: #000;
	opacity: .5;
}

.el-button {
	box-sizing: border-box;
	margin: 0;
	padding: 10px 15px;
	border: 1px solid #bfcbd9;
	border-radius: 4px;
	background: #fff;
	color: #1f2d3d;
	text-align: center;
	white-space: nowrap;
	font-size: 14px;
	line-height: 1;
	cursor: pointer;
}

.el-button+.el-button {
	margin-left: 10px;
}

.el-button:focus,.el-button:hover {
	border-color: #20a0ff;
	color: #20a0ff;
}

.el-button:active {
	outline: 0;
	border-color: #1d90e6;
	color: #1d90e6;
}

.el-button::-moz-focus-inner {
	border: 0;
}

.el-button [class*=el-icon-]+span {
	margin-left: 5px;
}

.el-button.is-loading {
	position: relative;
	pointer-events: none;
}

.el-button.is-loading:before {
	position: absolute;
	top: -1px;
	right: -1px;
	bottom: -1px;
	left: -1px;
	border-radius: inherit;
	background-color: hsla(0,0%,100%,.35);
	content: "";
	pointer-events: none;
}

.el-button.is-disabled,.el-button.is-disabled:focus,.el-button.is-disabled:hover {
	border-color: #d1dbe5;
	background-color: #eef1f6;
	background-image: none;
	color: #bfcbd9;
	cursor: not-allowed;
}

.el-button.is-disabled.el-button--text {
	background-color: transparent;
}

.el-button.is-disabled.is-plain,.el-button.is-disabled.is-plain:focus,.el-button.is-disabled.is-plain:hover {
	border-color: #d1dbe5;
	background-color: #fff;
	color: #bfcbd9;
}

.el-button.is-active {
	border-color: #1d90e6;
	color: #1d90e6;
}

.el-button.is-plain:focus,.el-button.is-plain:hover {
	border-color: #20a0ff;
	background: #fff;
	color: #20a0ff;
}

.el-button.is-plain:active {
	outline: 0;
	border-color: #1d90e6;
	background: #fff;
	color: #1d90e6;
}

.el-button--primary {
	border-color: #20a0ff;
	background-color: #20a0ff;
	color: #fff;
}

.el-button--primary:focus,.el-button--primary:hover {
	border-color: #4db3ff;
	background: #4db3ff;
	color: #fff;
}

.el-button--primary.is-active,.el-button--primary:active {
	border-color: #1d90e6;
	background: #1d90e6;
	color: #fff;
}

.el-button--primary:active {
	outline: 0;
}

.el-button--primary.is-plain {
	border: 1px solid #bfcbd9;
	background: #fff;
	color: #1f2d3d;
}

.el-button--primary.is-plain:focus,.el-button--primary.is-plain:hover {
	border-color: #20a0ff;
	background: #fff;
	color: #20a0ff;
}

.el-button--primary.is-plain:active {
	outline: 0;
	border-color: #1d90e6;
	background: #fff;
	color: #1d90e6;
}

.el-button--success {
	border-color: #13ce66;
	background-color: #13ce66;
	color: #fff;
}

.el-button--success:focus,.el-button--success:hover {
	border-color: #42d885;
	background: #42d885;
	color: #fff;
}

.el-button--success.is-active,.el-button--success:active {
	border-color: #11b95c;
	background: #11b95c;
	color: #fff;
}

.el-button--success:active {
	outline: 0;
}

.el-button--success.is-plain {
	border: 1px solid #bfcbd9;
	background: #fff;
	color: #1f2d3d;
}

.el-button--success.is-plain:focus,.el-button--success.is-plain:hover {
	border-color: #13ce66;
	background: #fff;
	color: #13ce66;
}

.el-button--success.is-plain:active {
	outline: 0;
	border-color: #11b95c;
	background: #fff;
	color: #11b95c;
}

.el-button--warning {
	border-color: #f7ba2a;
	background-color: #f7ba2a;
	color: #fff;
}

.el-button--warning:focus,.el-button--warning:hover {
	border-color: #f9c855;
	background: #f9c855;
	color: #fff;
}

.el-button--warning.is-active,.el-button--warning:active {
	border-color: #dea726;
	background: #dea726;
	color: #fff;
}

.el-button--warning:active {
	outline: 0;
}

.el-button--warning.is-plain {
	border: 1px solid #bfcbd9;
	background: #fff;
	color: #1f2d3d;
}

.el-button--warning.is-plain:focus,.el-button--warning.is-plain:hover {
	border-color: #f7ba2a;
	background: #fff;
	color: #f7ba2a;
}

.el-button--warning.is-plain:active {
	outline: 0;
	border-color: #dea726;
	background: #fff;
	color: #dea726;
}

.el-button--danger {
	border-color: #ff4949;
	background-color: #ff4949;
	color: #fff;
}

.el-button--danger:focus,.el-button--danger:hover {
	border-color: #ff6d6d;
	background: #ff6d6d;
	color: #fff;
}

.el-button--danger.is-active,.el-button--danger:active {
	border-color: #e64242;
	background: #e64242;
	color: #fff;
}

.el-button--danger:active {
	outline: 0;
}

.el-button--danger.is-plain {
	border: 1px solid #bfcbd9;
	background: #fff;
	color: #1f2d3d;
}

.el-button--danger.is-plain:focus,.el-button--danger.is-plain:hover {
	border-color: #ff4949;
	background: #fff;
	color: #ff4949;
}

.el-button--danger.is-plain:active {
	outline: 0;
	border-color: #e64242;
	background: #fff;
	color: #e64242;
}

.el-button--info {
	border-color: #50bfff;
	background-color: #50bfff;
	color: #fff;
}

.el-button--info:focus,.el-button--info:hover {
	border-color: #73ccff;
	background: #73ccff;
	color: #fff;
}

.el-button--info.is-active,.el-button--info:active {
	border-color: #48ace6;
	background: #48ace6;
	color: #fff;
}

.el-button--info:active {
	outline: 0;
}

.el-button--info.is-plain {
	border: 1px solid #bfcbd9;
	background: #fff;
	color: #1f2d3d;
}

.el-button--info.is-plain:focus,.el-button--info.is-plain:hover {
	border-color: #50bfff;
	background: #fff;
	color: #50bfff;
}

.el-button--info.is-plain:active {
	outline: 0;
	border-color: #48ace6;
	background: #fff;
	color: #48ace6;
}

.el-button--large {
	padding: 11px 19px;
	border-radius: 4px;
	font-size: 16px;
}

.el-button--small {
	padding: 7px 9px;
	border-radius: 4px;
	font-size: 12px;
}

.el-button--mini {
	padding: 4px;
	border-radius: 4px;
	font-size: 12px;
}

.el-button--text {
	padding-right: 0;
	padding-left: 0;
	border: none;
	background: 0 0;
	color: #20a0ff;
}

.el-button--text:focus,.el-button--text:hover {
	color: #4db3ff;
}

.el-button--text:active {
	color: #1d90e6;
}

.el-button-group {
	vertical-align: middle;
}

.el-button-group .el-button--primary:first-child {
	border-right-color: hsla(0,0%,100%,.5);
}

.el-button-group .el-button--primary:last-child {
	border-left-color: hsla(0,0%,100%,.5);
}

.el-button-group .el-button--primary:not(:first-child):not(:last-child) {
	border-left-color: hsla(0,0%,100%,.5);
	border-right-color: hsla(0,0%,100%,.5);
}

.el-button-group .el-button--success:first-child {
	border-right-color: hsla(0,0%,100%,.5);
}

.el-button-group .el-button--success:last-child {
	border-left-color: hsla(0,0%,100%,.5);
}

.el-button-group .el-button--success:not(:first-child):not(:last-child) {
	border-left-color: hsla(0,0%,100%,.5);
	border-right-color: hsla(0,0%,100%,.5);
}

.el-button-group .el-button--warning:first-child {
	border-right-color: hsla(0,0%,100%,.5);
}

.el-button-group .el-button--warning:last-child {
	border-left-color: hsla(0,0%,100%,.5);
}

.el-button-group .el-button--warning:not(:first-child):not(:last-child) {
	border-left-color: hsla(0,0%,100%,.5);
	border-right-color: hsla(0,0%,100%,.5);
}

.el-button-group .el-button--danger:first-child {
	border-right-color: hsla(0,0%,100%,.5);
}

.el-button-group .el-button--danger:last-child {
	border-left-color: hsla(0,0%,100%,.5);
}

.el-button-group .el-button--danger:not(:first-child):not(:last-child) {
	border-left-color: hsla(0,0%,100%,.5);
	border-right-color: hsla(0,0%,100%,.5);
}

.el-button-group .el-button--info:first-child {
	border-right-color: hsla(0,0%,100%,.5);
}

.el-button-group .el-button--info:last-child {
	border-left-color: hsla(0,0%,100%,.5);
}

.el-button-group .el-button--info:not(:first-child):not(:last-child) {
	border-left-color: hsla(0,0%,100%,.5);
	border-right-color: hsla(0,0%,100%,.5);
}

.el-button-group .el-button {
	position: relative;
	float: left;
}

.el-button-group .el-button+.el-button {
	margin-left: 0;
}

.el-button-group .el-button:first-child {
	border-top-right-radius: 0;
	border-bottom-right-radius: 0;
}

.el-button-group .el-button:last-child {
	border-bottom-left-radius: 0;
	border-top-left-radius: 0;
}

.el-button-group .el-button:not(:first-child):not(:last-child) {
	border-radius: 0;
}

.el-button-group .el-button:not(:last-child) {
	margin-right: -1px;
}

.el-button-group .el-button.is-active,.el-button-group .el-button:active,.el-button-group .el-button:focus,.el-button-group .el-button:hover {
	z-index: 1;
}

.el-message-box {
	overflow: hidden;
	width: 420px;
	border-radius: 3px;
	background-color: #fff;
	vertical-align: middle;
	text-align: left;
	font-size: 16px;
	-webkit-backface-visibility: hidden;
	backface-visibility: hidden;
}

.el-message-box__wrapper {
	position: fixed;
	top: 0;
	right: 0;
	bottom: 0;
	left: 0;
	text-align: center;
}

.el-message-box__wrapper:after {
	display: inline-block;
	width: 0;
	height: 100%;
	content: "";
	vertical-align: middle;
}

.el-message-box__header {
	position: relative;
	padding: 20px 20px 0;
}

.el-message-box__content {
	position: relative;
	padding: 30px 20px;
	color: #48576a;
	font-size: 14px;
}

.el-message-box__close {
	position: absolute;
	top: 19px;
	right: 20px;
	color: #999;
	text-align: center;
	line-height: 20px;
	cursor: pointer;
}

.el-message-box__close:hover {
	color: #20a0ff;
}

.el-message-box__input {
	padding-top: 15px;
}

.el-message-box__input input.invalid,.el-message-box__input input.invalid:focus {
	border-color: #ff4949;
}

.el-message-box__errormsg {
	margin-top: 2px;
	min-height: 18px;
	color: #ff4949;
	font-size: 12px;
}

.el-message-box__title {
	margin-bottom: 0;
	padding-left: 0;
	height: 18px;
	color: #333;
	font-weight: 700;
	font-size: 16px;
}

.el-message-box__message {
	margin: 0;
}

.el-message-box__message p {
	margin: 0;
	line-height: 1.4;
}

.el-message-box__btns {
	padding: 10px 20px 15px;
	text-align: right;
}

.el-message-box__btns button:nth-child(2) {
	margin-left: 10px;
}

.el-message-box__btns-reverse {
	-ms-flex-direction: row-reverse;
	-webkit-box-orient: horizontal;
	-webkit-box-direction: reverse;
	flex-direction: row-reverse;
}

.el-message-box__status {
	position: absolute;
	top: 50%;
	font-size: 36px!important;
	-webkit-transform: translateY(-50%);
	transform: translateY(-50%);
}

.el-message-box__status.el-icon-circle-check {
	color: #13ce66;
}

.el-message-box__status.el-icon-information {
	color: #50bfff;
}

.el-message-box__status.el-icon-warning {
	color: #f7ba2a;
}

.el-message-box__status.el-icon-circle-cross {
	color: #ff4949;
}

.msgbox-fade-enter-active {
	-webkit-animation: msgbox-fade-in .3s;
	animation: msgbox-fade-in .3s;
}

.msgbox-fade-leave-active {
	-webkit-animation: msgbox-fade-out .3s;
	animation: msgbox-fade-out .3s;
}

@-webkit-keyframes msgbox-fade-in {
	0% {
		opacity: 0;
		-webkit-transform: translate3d(0,-20px,0);
		transform: translate3d(0,-20px,0);
	}

	to {
		opacity: 1;
		-webkit-transform: translateZ(0);
		transform: translateZ(0);
	}
}

@keyframes msgbox-fade-in {
	0% {
		opacity: 0;
		-webkit-transform: translate3d(0,-20px,0);
		transform: translate3d(0,-20px,0);
	}

	to {
		opacity: 1;
		-webkit-transform: translateZ(0);
		transform: translateZ(0);
	}
}

@-webkit-keyframes msgbox-fade-out {
	0% {
		opacity: 1;
		-webkit-transform: translateZ(0);
		transform: translateZ(0);
	}

	to {
		opacity: 0;
		-webkit-transform: translate3d(0,-20px,0);
		transform: translate3d(0,-20px,0);
	}
}

@keyframes msgbox-fade-out {
	0% {
		opacity: 1;
		-webkit-transform: translateZ(0);
		transform: translateZ(0);
	}

	to {
		opacity: 0;
		-webkit-transform: translate3d(0,-20px,0);
		transform: translate3d(0,-20px,0);
	}
}

.el-date-table {
	min-width: 224px;
	font-size: 12px;
	-webkit-user-select: none;
	-moz-user-select: none;
	-ms-user-select: none;
	user-select: none;
}

.el-date-table td {
	box-sizing: border-box;
	width: 32px;
	height: 32px;
	text-align: center;
	cursor: pointer;
}

.el-month-table td .cell,.el-year-table td .cell {
	display: block;
	width: 48px;
	height: 32px;
	line-height: 32px;
}

.el-date-table td.next-month,.el-date-table td.prev-month {
	color: #ddd;
}

.el-date-table td.today {
	color: #20a0ff;
}

.el-date-table td.available:hover {
	background-color: #e4e8f1;
}

.el-date-table td.in-range {
	background-color: #d2ecff;
}

.el-date-table td.in-range:hover {
	background-color: #afddff;
}

.el-date-table td.current,.el-date-table td.end-date,.el-date-table td.start-date {
	background-color: #20a0ff!important;
	color: #fff;
}

.el-date-table td.disabled {
	background-color: #f4f4f4;
	color: #ccc;
	opacity: 1;
	cursor: not-allowed;
}

.el-fade-in-enter,.el-fade-in-leave-active,.fade-in-linear-enter,.fade-in-linear-leave,.fade-in-linear-leave-active {
	opacity: 0;
}

.el-date-table td.week {
	color: #8391a5;
	font-size: 80%;
}

.el-month-table,.el-year-table {
	margin: -1px;
	border-collapse: collapse;
	font-size: 12px;
}

.el-date-table th {
	padding: 5px;
	color: #8391a5;
	font-weight: 400;
}

.el-date-table.is-week-mode .el-date-table__row:hover {
	background-color: #e4e8f1;
}

.el-date-table.is-week-mode .el-date-table__row.current {
	background-color: #d2ecff;
}

.el-month-table td {
	padding: 20px 3px;
	text-align: center;
	cursor: pointer;
}

.el-month-table td .cell {
	color: #48576a;
}

.el-month-table td .cell:hover {
	background-color: #e4e8f1;
}

.el-month-table td.disabled .cell {
	background-color: #f4f4f4;
	color: #ccc;
	cursor: not-allowed;
}

.el-month-table td.current .cell {
	background-color: #20a0ff!important;
	color: #fff;
}

.el-year-table .el-icon {
	color: #97a8be;
}

.el-year-table td {
	padding: 20px 3px;
	text-align: center;
	cursor: pointer;
}

.el-year-table td .cell {
	color: #48576a;
}

.el-year-table td .cell:hover {
	background-color: #e4e8f1;
}

.el-year-table td.disabled .cell {
	background-color: #f4f4f4;
	color: #ccc;
	cursor: not-allowed;
}

.el-year-table td.current .cell {
	background-color: #20a0ff!important;
	color: #fff;
}

.el-date-range-picker {
	min-width: 520px;
}

.el-date-range-picker table {
	width: 100%;
	table-layout: fixed;
}

.el-date-range-picker .el-picker-panel__body {
	min-width: 513px;
}

.el-date-range-picker .el-picker-panel__content {
	margin: 0;
}

.el-date-range-picker.has-sidebar.has-time {
	min-width: 766px;
}

.el-date-range-picker.has-sidebar {
	min-width: 620px;
}

.el-date-range-picker.has-time {
	min-width: 660px;
}

.el-date-range-picker__header {
	position: relative;
	height: 28px;
	text-align: center;
}

.el-date-range-picker__header button {
	float: left;
}

.el-date-range-picker__header div {
	margin-right: 50px;
	font-size: 14px;
}

.el-date-range-picker__content {
	float: left;
	box-sizing: border-box;
	margin: 0;
	padding: 16px;
	width: 50%;
}

.el-date-range-picker__content.is-right .el-date-range-picker__header button {
	float: right;
}

.el-date-range-picker__content.is-right .el-date-range-picker__header div {
	margin-right: 50px;
	margin-left: 50px;
}

.el-date-range-picker__content.is-left {
	border-right: 1px solid #e4e4e4;
}

.el-date-range-picker__editors-wrap {
	display: table-cell;
	box-sizing: border-box;
}

.el-date-range-picker__editors-wrap.is-right {
	text-align: right;
}

.el-date-range-picker__time-header {
	position: relative;
	display: table;
	box-sizing: border-box;
	padding: 8px 5px 5px;
	width: 100%;
	border-bottom: 1px solid #e4e4e4;
	font-size: 12px;
}

.el-date-range-picker__time-header>.el-icon-arrow-right {
	display: table-cell;
	color: #97a8be;
	vertical-align: middle;
	font-size: 20px;
}

.el-date-range-picker__time-picker-wrap {
	position: relative;
	display: table-cell;
	padding: 0 5px;
}

.el-date-range-picker__time-picker-wrap .el-picker-panel {
	position: absolute;
	top: 13px;
	right: 0;
	z-index: 1;
	background: #fff;
}

.el-time-range-picker {
	overflow: visible;
	min-width: 354px;
}

.el-time-range-picker__content {
	position: relative;
	padding: 10px;
	text-align: center;
}

.el-time-range-picker__cell {
	display: inline-block;
	box-sizing: border-box;
	margin: 0;
	padding: 4px 7px 7px;
	width: 50%;
}

.el-time-range-picker__header {
	margin-bottom: 5px;
	text-align: center;
	font-size: 14px;
}

.el-time-range-picker__body {
	border: 1px solid #d1dbe5;
	border-radius: 2px;
}

.el-time-spinner.has-seconds .el-time-spinner__wrapper {
	width: 33%;
}

.el-time-spinner.has-seconds .el-time-spinner__wrapper:nth-child(2) {
	padding-left: 1%;
}

.el-time-spinner__wrapper {
	position: relative;
	display: inline-block;
	overflow: auto;
	width: 50%;
	max-height: 190px;
	vertical-align: top;
}

.el-time-spinner__list {
	margin: 0;
	padding: 0;
	list-style: none;
	text-align: center;
}

.el-time-spinner__list:after,.el-time-spinner__list:before {
	display: block;
	width: 100%;
	height: 80px;
	content: "";
}

.el-time-spinner__item {
	height: 32px;
	font-size: 12px;
	line-height: 32px;
}

.el-time-spinner__item:hover:not(.disabled):not(.active) {
	background: #e4e8f1;
	cursor: pointer;
}

.el-time-spinner__item.active:not(.disabled) {
	color: #fff;
}

.el-time-spinner__item.disabled {
	color: #d1dbe5;
	cursor: not-allowed;
}

.el-time-panel {
	position: absolute;
	left: 0;
	z-index: 1000;
	margin: 5px 0;
	width: 180px;
	border: 1px solid #d1dbe5;
	border-radius: 2px;
	background-color: #fff;
	-webkit-user-select: none;
	-moz-user-select: none;
	-ms-user-select: none;
	user-select: none;
}

.el-time-panel__content {
	position: relative;
	overflow: hidden;
	font-size: 0;
}

.el-time-panel__content:after,.el-time-panel__content:before {
	position: absolute;
	top: 50%;
	right: 0;
	left: 0;
	z-index: -1;
	box-sizing: border-box;
	margin-top: -15px;
	padding-top: 6px;
	height: 32px;
	background-color: #20a0ff;
	color: #fff;
	content: ":";
	text-align: left;
	font-size: 14px;
	line-height: 16px;
}

.el-time-panel__content:after {
	left: 50%;
	margin-left: -2px;
}

.el-time-panel__content:before {
	margin-right: -2px;
	padding-left: 50%;
}

.el-time-panel__content.has-seconds:after {
	left: 66.66667%;
}

.el-time-panel__content.has-seconds:before {
	padding-left: 33.33333%;
}

.el-time-panel__footer {
	box-sizing: border-box;
	padding: 4px;
	height: 36px;
	border-top: 1px solid #e4e4e4;
	text-align: right;
	line-height: 25px;
}

.el-time-panel__btn {
	margin: 0 5px;
	padding: 0 5px;
	outline: 0;
	border: none;
	background-color: transparent;
	color: #8391a5;
	font-size: 12px;
	line-height: 28px;
	cursor: pointer;
}

.el-time-panel__btn.confirm {
	color: #20a0ff;
	font-weight: 800;
}

.fade-in-linear-enter-active,.fade-in-linear-leave-active {
	transition: opacity .2s linear;
}

.el-fade-in-enter-active,.el-fade-in-leave-active,.el-zoom-in-center-enter-active,.el-zoom-in-center-leave-active {
	transition: all .3s cubic-bezier(.55,0,.1,1);
}

.el-zoom-in-center-enter,.el-zoom-in-center-leave-active {
	opacity: 0;
	-webkit-transform: scaleX(0);
	transform: scaleX(0);
}

.el-zoom-in-top-enter-active,.el-zoom-in-top-leave-active {
	opacity: 1;
	transition: opacity .3s cubic-bezier(.23,1,.32,1) .1s,-webkit-transform .3s cubic-bezier(.23,1,.32,1) .1s;
	transition: transform .3s cubic-bezier(.23,1,.32,1) .1s,opacity .3s cubic-bezier(.23,1,.32,1) .1s;
	transition: transform .3s cubic-bezier(.23,1,.32,1) .1s,opacity .3s cubic-bezier(.23,1,.32,1) .1s,-webkit-transform .3s cubic-bezier(.23,1,.32,1) .1s;
	-webkit-transform: scaleY(1);
	transform: scaleY(1);
	-webkit-transform-origin: center top;
	transform-origin: center top;
}

.el-zoom-in-top-enter,.el-zoom-in-top-leave-active {
	opacity: 0;
	-webkit-transform: scaleY(0);
	transform: scaleY(0);
}

.el-zoom-in-bottom-enter-active,.el-zoom-in-bottom-leave-active {
	opacity: 1;
	transition: opacity .3s cubic-bezier(.23,1,.32,1) .1s,-webkit-transform .3s cubic-bezier(.23,1,.32,1) .1s;
	transition: transform .3s cubic-bezier(.23,1,.32,1) .1s,opacity .3s cubic-bezier(.23,1,.32,1) .1s;
	transition: transform .3s cubic-bezier(.23,1,.32,1) .1s,opacity .3s cubic-bezier(.23,1,.32,1) .1s,-webkit-transform .3s cubic-bezier(.23,1,.32,1) .1s;
	-webkit-transform: scaleY(1);
	transform: scaleY(1);
	-webkit-transform-origin: center bottom;
	transform-origin: center bottom;
}

.el-zoom-in-bottom-enter,.el-zoom-in-bottom-leave-active {
	opacity: 0;
	-webkit-transform: scaleY(0);
	transform: scaleY(0);
}

.el-date-editor {
	position: relative;
	display: inline-block;
}

.el-date-editor .el-picker-panel {
	position: absolute;
	top: 41px;
	z-index: 10;
	box-sizing: border-box;
	min-width: 180px;
	background: #fff;
	box-shadow: 0 2px 6px #ccc;
}

.el-date-editor.el-input {
	width: 193px;
}

.el-date-editor--daterange.el-input {
	width: 220px;
}

.el-date-editor--datetimerange.el-input {
	width: 350px;
}

.el-picker-panel {
	margin: 5px 0;
	border: 1px solid #d1dbe5;
	border-radius: 2px;
	background: #fff;
	box-shadow: 0 2px 6px #ccc;
	color: #48576a;
	line-height: 20px;
}

.el-card,.el-menu--horizontal .el-submenu>.el-menu,.el-tabs--border-card {
	box-shadow: 0 2px 4px 0 rgba(0,0,0,.12),0 0 6px 0 rgba(0,0,0,.04);
}

.el-picker-panel__body-wrapper:after,.el-picker-panel__body:after {
	display: table;
	content: "";
}

.el-picker-panel__content {
	position: relative;
	margin: 15px;
}

.el-picker-panel__footer {
	position: relative;
	padding: 4px;
	border-top: 1px solid #e4e4e4;
	background-color: #fff;
	text-align: right;
}

.el-picker-panel__shortcut {
	display: block;
	padding-left: 12px;
	width: 100%;
	outline: 0;
	border: 0;
	background-color: transparent;
	color: #48576a;
	text-align: left;
	font-size: 14px;
	line-height: 28px;
	cursor: pointer;
}

.el-picker-panel__shortcut:hover {
	background-color: #e4e8f1;
}

.el-picker-panel__shortcut.active {
	background-color: #e6f1fe;
	color: #20a0ff;
}

.el-picker-panel__btn {
	padding: 0 20px;
	outline: 0;
	border: 1px solid #dcdcdc;
	border-radius: 2px;
	background-color: transparent;
	color: #333;
	font-size: 12px;
	line-height: 24px;
	cursor: pointer;
}

.el-picker-panel__btn[disabled] {
	color: #ccc;
	cursor: not-allowed;
}

.el-picker-panel__icon-btn {
	margin-top: 3px;
	outline: 0;
	border: 0;
	background: 0 0;
	color: #97a8be;
	font-size: 12px;
	cursor: pointer;
}

.el-date-picker__header-label.active,.el-date-picker__header-label:hover,.el-picker-panel__icon-btn:hover {
	color: #20a0ff;
}

.el-input__inner,.el-textarea__inner {
	box-sizing: border-box;
	background-image: none;
}

.el-picker-panel__link-btn {
	padding: 15px;
	color: #20a0ff;
	text-decoration: none;
	font-size: 12px;
	cursor: pointer;
}

.el-picker-panel [slot=sidebar],.el-picker-panel__sidebar {
	position: absolute;
	top: 0;
	bottom: 0;
	box-sizing: border-box;
	padding-top: 6px;
	width: 110px;
	border-right: 1px solid #e4e4e4;
	background-color: #fbfdff;
}

.el-picker-panel [slot=sidebar]+.el-picker-panel__body,.el-picker-panel__sidebar+.el-picker-panel__body {
	margin-left: 110px;
}

.el-date-picker {
	min-width: 254px;
}

.el-date-picker .el-picker-panel__content {
	min-width: 224px;
}

.el-date-picker table {
	width: 100%;
	table-layout: fixed;
}

.el-date-picker.has-sidebar.has-time {
	min-width: 434px;
}

.el-date-picker.has-sidebar {
	min-width: 370px;
}

.el-date-picker.has-time {
	min-width: 324px;
}

.el-date-picker__editor-wrap {
	position: relative;
	display: table-cell;
	padding: 0 5px;
}

.el-date-picker__time-header {
	position: relative;
	display: table;
	box-sizing: border-box;
	padding: 8px 5px 5px;
	width: 100%;
	border-bottom: 1px solid #e4e4e4;
	font-size: 12px;
}

.el-date-picker__header {
	margin: 12px;
	text-align: center;
}

.el-date-picker__header-label {
	padding: 0 5px;
	text-align: center;
	font-size: 14px;
	line-height: 22px;
	cursor: pointer;
}

.el-date-picker__prev-btn {
	float: left;
}

.el-date-picker__next-btn {
	float: right;
}

.el-date-picker__time-wrap {
	padding: 10px;
	text-align: center;
}

.el-date-picker__time-label {
	float: left;
	margin-left: 10px;
	line-height: 30px;
	cursor: pointer;
}

.time-select {
	margin: 5px 0;
	min-width: 0;
}

.time-select .el-picker-panel__content {
	margin: 0;
	max-height: 200px;
}

.time-select-item {
	padding: 8px 10px;
	font-size: 14px;
}

.time-select-item.selected:not(.disabled) {
	background-color: #20a0ff;
	color: #fff;
}

.time-select-item.selected:not(.disabled):hover {
	background-color: #20a0ff;
}

.time-select-item.disabled {
	color: #d1dbe5;
	cursor: not-allowed;
}

.time-select-item:hover {
	background-color: #e4e8f1;
	cursor: pointer;
}

.el-input {
	position: relative;
	display: inline-block;
	width: 100%;
	font-size: 14px;
}

.el-input.is-disabled .el-input__inner {
	border-color: #d1dbe5;
	background-color: #eef1f6;
	color: #bbb;
	cursor: not-allowed;
}

.el-input.is-disabled .el-input__inner::-webkit-input-placeholder {
	color: #bfcbd9;
}

.el-input.is-disabled .el-input__inner:-ms-input-placeholder {
	color: #bfcbd9;
}

.el-input.is-disabled .el-input__inner::placeholder {
	color: #bfcbd9;
}

.el-input.is-active .el-input__inner {
	outline: 0;
	border-color: #20a0ff;
}

.el-input__inner {
	display: block;
	padding: 3px 10px;
	width: 100%;
	height: 36px;
	border: 1px solid #bfcbd9;
	border-radius: 4px;
	background-color: #fff;
	color: #1f2d3d;
	font-size: inherit;
	line-height: 1;
	transition: border-color .2s cubic-bezier(.645,.045,.355,1);
	-moz-appearance: none;
	-webkit-appearance: none;
	appearance: none;
}

.el-input__inner::-webkit-input-placeholder {
	color: #97a8be;
}

.el-input__inner:-ms-input-placeholder {
	color: #97a8be;
}

.el-input__inner::placeholder {
	color: #97a8be;
}

.el-input__inner:hover {
	border-color: #8391a5;
}

.el-input__inner:focus {
	outline: 0;
	border-color: #20a0ff;
}

.el-input__icon {
	position: absolute;
	top: 0;
	right: 0;
	width: 35px;
	height: 100%;
	color: #bfcbd9;
	text-align: center;
	transition: all .3s;
}

.el-input__icon:after {
	display: inline-block;
	width: 0;
	height: 100%;
	content: "";
	vertical-align: middle;
}

.el-input__icon+.el-input__inner {
	padding-right: 35px;
}

.el-input__icon.is-clickable:hover {
	color: #8391a5;
	cursor: pointer;
}

.el-input__icon.is-clickable:hover+.el-input__inner {
	border-color: #8391a5;
}

.el-input--large {
	font-size: 16px;
}

.el-input--large .el-input__inner {
	height: 42px;
}

.el-input--small {
	font-size: 13px;
}

.el-input--small .el-input__inner {
	height: 30px;
}

.el-input--mini {
	font-size: 12px;
}

.el-input--mini .el-input__inner {
	height: 22px;
}

.el-input-group {
	display: inline-table;
	width: 100%;
	border-collapse: separate;
	line-height: normal;
}

.el-input-group>.el-input__inner {
	display: table-cell;
	vertical-align: middle;
}

.el-input-group__append,.el-input-group__prepend {
	position: relative;
	display: table-cell;
	padding: 0 10px;
	width: 1%;
	border: 1px solid #bfcbd9;
	border-radius: 4px;
	background-color: #fbfdff;
	color: #97a8be;
	vertical-align: middle;
	white-space: nowrap;
}

.el-input-group--prepend .el-input__inner,.el-input-group__append {
	border-bottom-left-radius: 0;
	border-top-left-radius: 0;
}

.el-input-group--append .el-input__inner,.el-input-group__prepend {
	border-top-right-radius: 0;
	border-bottom-right-radius: 0;
}

.el-input-group__append .el-button,.el-input-group__append .el-select,.el-input-group__prepend .el-button,.el-input-group__prepend .el-select {
	display: block;
	margin: -10px;
}

.el-input-group__append .el-button,.el-input-group__append .el-select .el-input__inner,.el-input-group__append .el-select:hover .el-input__inner,.el-input-group__prepend .el-button,.el-input-group__prepend .el-select .el-input__inner,.el-input-group__prepend .el-select:hover .el-input__inner {
	border-color: transparent;
	border-top: 0;
	border-bottom: 0;
	background-color: transparent;
	color: inherit;
}

.el-input-group__append .el-button,.el-input-group__append .el-input,.el-input-group__prepend .el-button,.el-input-group__prepend .el-input {
	font-size: inherit;
}

.el-input-group__prepend {
	border-right: 0;
}

.el-input-group__append {
	border-left: 0;
}

.el-textarea {
	display: inline-block;
	width: 100%;
	vertical-align: bottom;
}

.el-textarea.is-disabled .el-textarea__inner {
	border-color: #d1dbe5;
	background-color: #eef1f6;
	color: #bbb;
	cursor: not-allowed;
}

.el-textarea.is-disabled .el-textarea__inner::-webkit-input-placeholder {
	color: #bfcbd9;
}

.el-textarea.is-disabled .el-textarea__inner:-ms-input-placeholder {
	color: #bfcbd9;
}

.el-textarea.is-disabled .el-textarea__inner::placeholder {
	color: #bfcbd9;
}

.el-textarea__inner {
	display: block;
	padding: 5px 7px;
	width: 100%;
	border: 1px solid #bfcbd9;
	border-radius: 4px;
	background-color: #fff;
	color: #1f2d3d;
	font-size: 14px;
	line-height: 1.5;
	resize: vertical;
	transition: border-color .2s cubic-bezier(.645,.045,.355,1);
}

.el-textarea__inner::-webkit-input-placeholder {
	color: #97a8be;
}

.el-textarea__inner:-ms-input-placeholder {
	color: #97a8be;
}

.el-textarea__inner::placeholder {
	color: #97a8be;
}

.el-textarea__inner:hover {
	border-color: #8391a5;
}

.el-textarea__inner:focus {
	outline: 0;
	border-color: #20a0ff;
}

.el-input-number {
	position: relative;
	display: inline-block;
	overflow: hidden;
	width: 180px;
}

.el-input-number .el-input__inner {
	padding-right: 82px;
	-webkit-appearance: none;
	-moz-appearance: none;
	appearance: none;
}

.el-input-number.is-without-controls .el-input__inner {
	padding-right: 10px;
}

.el-input-number.is-disabled .el-input-number__decrease,.el-input-number.is-disabled .el-input-number__increase {
	border-color: #d1dbe5;
	color: #d1dbe5;
}

.el-input-number.is-disabled .el-input-number__decrease:hover,.el-input-number.is-disabled .el-input-number__increase:hover {
	color: #d1dbe5;
	cursor: not-allowed;
}

.el-input-number__decrease,.el-input-number__increase {
	position: absolute;
	top: 1px;
	z-index: 1;
	width: 36px;
	height: auto;
	border-left: 1px solid #bfcbd9;
	color: #97a8be;
	text-align: center;
	line-height: 34px;
	cursor: pointer;
}

.el-input-number__decrease:hover,.el-input-number__increase:hover {
	color: #20a0ff;
}

.el-input-number__decrease:hover:not(.is-disabled)~.el-input .el-input__inner:not(.is-disabled),.el-input-number__increase:hover:not(.is-disabled)~.el-input .el-input__inner:not(.is-disabled) {
	border-color: #20a0ff;
}

.el-input-number__decrease.is-disabled,.el-input-number__increase.is-disabled {
	color: #d1dbe5;
	cursor: not-allowed;
}

.el-input-number__increase {
	right: 0;
}

.el-input-number__decrease {
	right: 37px;
}

.el-input-number--large {
	width: 200px;
}

.el-input-number--large .el-input-number__decrease,.el-input-number--large .el-input-number__increase {
	width: 42px;
	font-size: 16px;
	line-height: 42px;
}

.el-input-number--large .el-input-number__decrease {
	right: 43px;
}

.el-input-number--large .el-input__inner {
	padding-right: 94px;
}

.el-input-number--small {
	width: 130px;
}

.el-input-number--small .el-input-number__decrease,.el-input-number--small .el-input-number__increase {
	width: 30px;
	font-size: 13px;
	line-height: 30px;
}

.el-input-number--small .el-input-number__decrease {
	right: 31px;
}

.el-input-number--small .el-input__inner {
	padding-right: 70px;
}

.el-tag {
	display: inline-block;
	box-sizing: border-box;
	padding: 0 5px;
	height: 24px;
	border: 1px solid transparent;
	border-radius: 4px;
	background-color: #8391a5;
	color: #fff;
	white-space: nowrap;
	font-size: 12px;
	line-height: 22px;
}

.el-tag .el-icon-close {
	position: relative;
	top: -1px;
	right: -2px;
	width: 18px;
	height: 18px;
	border-radius: 50%;
	vertical-align: middle;
	text-align: center;
	font-size: 12px;
	line-height: 18px;
	cursor: pointer;
	-webkit-transform: scale(.75);
	transform: scale(.75);
}

.el-tag .el-icon-close:hover {
	background-color: #fff;
	color: #8391a5;
}

.el-tag--gray {
	border-color: #e4e8f1;
	background-color: #e4e8f1;
	color: #48576a;
}

.el-tag--gray .el-tag__close:hover {
	background-color: #48576a;
	color: #fff;
}

.el-tag--gray.is-hit {
	border-color: #48576a;
}

.el-tag--primary {
	border-color: rgba(32,159,255,.2);
	background-color: rgba(32,159,255,.1);
	color: #20a0ff;
}

.el-tag--primary .el-tag__close:hover {
	background-color: #20a0ff;
	color: #fff;
}

.el-tag--primary.is-hit {
	border-color: #20a0ff;
}

.el-tag--success {
	border-color: rgba(18,206,102,.2);
	background-color: rgba(18,206,102,.1);
	color: #13ce66;
}

.el-tag--success .el-tag__close:hover {
	background-color: #13ce66;
	color: #fff;
}

.el-tag--success.is-hit {
	border-color: #13ce66;
}

.el-tag--warning {
	border-color: rgba(247,186,41,.2);
	background-color: rgba(247,186,41,.1);
	color: #f7ba2a;
}

.el-tag--warning .el-tag__close:hover {
	background-color: #f7ba2a;
	color: #fff;
}

.el-tag--warning.is-hit {
	border-color: #f7ba2a;
}

.el-tag--danger {
	border-color: rgba(255,73,73,.2);
	background-color: rgba(255,73,73,.1);
	color: #ff4949;
}

.el-form-item.is-error .el-input__inner,.el-form-item.is-error .el-textarea__inner,.el-tag--danger.is-hit {
	border-color: #ff4949;
}

.el-tag--danger .el-tag__close:hover {
	background-color: #ff4949;
	color: #fff;
}

.el-breadcrumb {
	font-size: 13px;
	line-height: 1;
}

.el-breadcrumb__separator {
	margin: 0 8px;
	color: #bfcbd9;
}

.el-breadcrumb__item {
	float: left;
}

.el-breadcrumb__item:last-child .el-breadcrumb__item__inner,.el-breadcrumb__item:last-child .el-breadcrumb__item__inner:hover,.el-breadcrumb__item:last-child .el-breadcrumb__item__inner a,.el-breadcrumb__item:last-child .el-breadcrumb__item__inner a:hover {
	color: #97a8be;
	cursor: text;
}

.el-breadcrumb__item:last-child .el-breadcrumb__separator {
	display: none;
}

.el-breadcrumb__item__inner,.el-breadcrumb__item__inner a {
	color: #48576a;
	transition: color .15s linear;
}

.el-breadcrumb__item__inner:hover,.el-breadcrumb__item__inner a:hover {
	color: #20a0ff;
	cursor: pointer;
}

.el-form--label-left .el-form-item__label {
	text-align: left;
}

.el-form--label-top .el-form-item__label {
	float: none;
	display: inline-block;
	padding: 0 0 10px;
}

.el-form--inline .el-form-item {
	display: inline-block;
	margin-right: 10px;
	vertical-align: top;
}

.el-form-item {
	margin-bottom: 22px;
}

.el-form-item .el-form-item {
	margin-bottom: 0;
}

.el-form-item .el-form-item .el-form-item__content {
	margin-left: 0!important;
}

.el-form-item.is-required .el-form-item__label:before {
	margin-right: 4px;
	color: #ff4949;
	content: "*";
}

.el-dragger__cover:after,.el-menu:after,.el-menu:before,.el-progress-bar__inner:after,.el-row:after,.el-row:before {
	content: "";
}

.el-form-item__label {
	float: left;
	box-sizing: border-box;
	padding: 11px 12px 11px 0;
	color: #48576a;
	vertical-align: middle;
	text-align: right;
	font-size: 14px;
	line-height: 1;
}

.el-form-item__content {
	position: relative;
	font-size: 14px;
	line-height: 36px;
}

.el-form-item__error {
	position: absolute;
	top: 100%;
	left: 0;
	padding-top: 4px;
	color: #ff4949;
	font-size: 12px;
	line-height: 1;
}

.el-tabs__header {
	position: relative;
	margin: 0 0 15px;
	padding: 0;
	border-bottom: 1px solid #d1dbe5;
}

.el-tabs__header:after,.el-tabs__header:before {
	display: table;
	content: "";
}

.el-tabs__active-bar {
	position: absolute;
	bottom: 0;
	left: 0;
	z-index: 1;
	height: 3px;
	background-color: #20a0ff;
	list-style: none;
	transition: -webkit-transform .3s cubic-bezier(.645,.045,.355,1);
	transition: transform .3s cubic-bezier(.645,.045,.355,1);
	transition: transform .3s cubic-bezier(.645,.045,.355,1),-webkit-transform .3s cubic-bezier(.645,.045,.355,1);
}

.el-tabs__item {
	position: relative;
	float: left;
	box-sizing: border-box;
	margin-bottom: -1px;
	padding: 0 16px;
	height: 42px;
	color: #8391a5;
	list-style: none;
	font-size: 14px;
	line-height: 42px;
}

.el-tabs__item .el-icon-close {
	margin-left: 5px;
	border-radius: 50%;
	text-align: center;
	transition: all .3s cubic-bezier(.645,.045,.355,1);
}

.el-tabs__item .el-icon-close:before {
	display: inline-block;
	-webkit-transform: scale(.7);
	transform: scale(.7);
}

.el-tabs__item .el-icon-close:hover {
	background-color: #97a8be;
	color: #fff;
}

.el-tabs__item:hover {
	color: #1f2d3d;
	cursor: pointer;
}

.el-tabs__item.is-disabled {
	color: #bbb;
	cursor: default;
}

.el-tabs__item.is-active {
	color: #20a0ff;
}

.el-tabs__content {
	position: relative;
	overflow: hidden;
}

.el-tabs--card>.el-tabs__header>.el-tabs__active-bar {
	display: none;
}

.el-tabs--card>.el-tabs__header>.el-tabs__item .el-icon-close {
	position: relative;
	top: -1px;
	right: -2px;
	overflow: hidden;
	width: 0;
	height: 14px;
	vertical-align: middle;
	font-size: 12px;
	line-height: 15px;
	-webkit-transform-origin: 100% 50%;
	transform-origin: 100% 50%;
}

.el-tabs--card>.el-tabs__header>.el-tabs__item.is-active.is-closable .el-icon-close,.el-tabs--card>.el-tabs__header>.el-tabs__item.is-closable:hover .el-icon-close {
	width: 14px;
}

.el-tabs--card>.el-tabs__header>.el-tabs__item {
	border: 1px solid transparent;
	transition: all .3s cubic-bezier(.645,.045,.355,1);
}

.el-tabs--card>.el-tabs__header>.el-tabs__item.is-closable:hover {
	padding-right: 9px;
	padding-left: 9px;
}

.el-tabs--card>.el-tabs__header>.el-tabs__item.is-active {
	border: 1px solid #d1dbe5;
	border-radius: 4px 4px 0 0;
	border-bottom-color: #fff;
}

.el-tabs--card>.el-tabs__header>.el-tabs__item.is-active.is-closable {
	padding-right: 16px;
	padding-left: 16px;
}

.el-tabs--border-card {
	border: 1px solid #d1dbe5;
	background: #fff;
}

.el-tabs--border-card>.el-tabs__content {
	padding: 15px;
}

.el-tabs--border-card>.el-tabs__header {
	margin: 0;
	background-color: #eef1f6;
}

.el-tabs--border-card>.el-tabs__header>.el-tabs__item {
	margin-right: -1px;
	margin-left: -1px;
	border: 1px solid transparent;
	border-top: 0;
	transition: all .3s cubic-bezier(.645,.045,.355,1);
}

.el-tabs--border-card>.el-tabs__header>.el-tabs__item.is-active {
	background-color: #fff;
	border-right-color: #d1dbe5;
	border-left-color: #d1dbe5;
}

.el-tabs--border-card>.el-tabs__header>.el-tabs__item.is-active:first-child {
	border-left-color: #d1dbe5;
}

.el-tabs--border-card>.el-tabs__header>.el-tabs__item.is-active:last-child {
	border-right-color: #d1dbe5;
}

.slideInLeft-transition,.slideInRight-transition {
	display: inline-block;
}

.slideInRight-enter {
	-webkit-animation: slideInRight-enter .3s;
	animation: slideInRight-enter .3s;
}

.slideInRight-leave {
	position: absolute;
	right: 0;
	left: 0;
	-webkit-animation: slideInRight-leave .3s;
	animation: slideInRight-leave .3s;
}

.slideInLeft-enter {
	-webkit-animation: slideInLeft-enter .3s;
	animation: slideInLeft-enter .3s;
}

.slideInLeft-leave {
	position: absolute;
	right: 0;
	left: 0;
	-webkit-animation: slideInLeft-leave .3s;
	animation: slideInLeft-leave .3s;
}

@-webkit-keyframes slideInRight-enter {
	0% {
		opacity: 0;
		-webkit-transform: translateX(100%);
		transform: translateX(100%);
		-webkit-transform-origin: 0 0;
		transform-origin: 0 0;
	}

	to {
		opacity: 1;
		-webkit-transform: translateX(0);
		transform: translateX(0);
		-webkit-transform-origin: 0 0;
		transform-origin: 0 0;
	}
}

@keyframes slideInRight-enter {
	0% {
		opacity: 0;
		-webkit-transform: translateX(100%);
		transform: translateX(100%);
		-webkit-transform-origin: 0 0;
		transform-origin: 0 0;
	}

	to {
		opacity: 1;
		-webkit-transform: translateX(0);
		transform: translateX(0);
		-webkit-transform-origin: 0 0;
		transform-origin: 0 0;
	}
}

@-webkit-keyframes slideInRight-leave {
	0% {
		opacity: 1;
		-webkit-transform: translateX(0);
		transform: translateX(0);
		-webkit-transform-origin: 0 0;
		transform-origin: 0 0;
	}

	to {
		opacity: 0;
		-webkit-transform: translateX(100%);
		transform: translateX(100%);
		-webkit-transform-origin: 0 0;
		transform-origin: 0 0;
	}
}

@keyframes slideInRight-leave {
	0% {
		opacity: 1;
		-webkit-transform: translateX(0);
		transform: translateX(0);
		-webkit-transform-origin: 0 0;
		transform-origin: 0 0;
	}

	to {
		opacity: 0;
		-webkit-transform: translateX(100%);
		transform: translateX(100%);
		-webkit-transform-origin: 0 0;
		transform-origin: 0 0;
	}
}

@-webkit-keyframes slideInLeft-enter {
	0% {
		opacity: 0;
		-webkit-transform: translateX(-100%);
		transform: translateX(-100%);
		-webkit-transform-origin: 0 0;
		transform-origin: 0 0;
	}

	to {
		opacity: 1;
		-webkit-transform: translateX(0);
		transform: translateX(0);
		-webkit-transform-origin: 0 0;
		transform-origin: 0 0;
	}
}

@keyframes slideInLeft-enter {
	0% {
		opacity: 0;
		-webkit-transform: translateX(-100%);
		transform: translateX(-100%);
		-webkit-transform-origin: 0 0;
		transform-origin: 0 0;
	}

	to {
		opacity: 1;
		-webkit-transform: translateX(0);
		transform: translateX(0);
		-webkit-transform-origin: 0 0;
		transform-origin: 0 0;
	}
}

@-webkit-keyframes slideInLeft-leave {
	0% {
		opacity: 1;
		-webkit-transform: translateX(0);
		transform: translateX(0);
		-webkit-transform-origin: 0 0;
		transform-origin: 0 0;
	}

	to {
		opacity: 0;
		-webkit-transform: translateX(-100%);
		transform: translateX(-100%);
		-webkit-transform-origin: 0 0;
		transform-origin: 0 0;
	}
}

@keyframes slideInLeft-leave {
	0% {
		opacity: 1;
		-webkit-transform: translateX(0);
		transform: translateX(0);
		-webkit-transform-origin: 0 0;
		transform-origin: 0 0;
	}

	to {
		opacity: 0;
		-webkit-transform: translateX(-100%);
		transform: translateX(-100%);
		-webkit-transform-origin: 0 0;
		transform-origin: 0 0;
	}
}

.el-progress-bar__inner:after {
	display: inline-block;
	height: 100%;
	vertical-align: middle;
}

.el-tree {
	border: 1px solid #d1dbe5;
	background: #fff;
	cursor: default;
}

.el-tree__empty-block {
	position: relative;
	width: 100%;
	height: 100%;
	min-height: 60px;
	text-align: center;
}

.el-tree__empty-text {
	position: absolute;
	top: 50%;
	left: 50%;
	color: #5e7382;
	-webkit-transform: translate(-50%,-50%);
	transform: translate(-50%,-50%);
}

.el-tree-node {
	white-space: nowrap;
}

.el-tree-node>.el-tree-node__children {
	overflow: hidden;
	background-color: transparent;
}

.el-tree-node.is-expanded>.el-tree-node__children {
	display: block;
}

.el-tree-node__expand-icon,.el-tree-node__label,.el-tree-node__loading-icon {
	display: inline-block;
	vertical-align: middle;
}

.el-tree-node__content {
	height: 36px;
	line-height: 36px;
	cursor: pointer;
}

.el-tree-node__content>.el-checkbox,.el-tree-node__content>.el-tree-node__expand-icon {
	margin-right: 8px;
}

.el-tree-node__content>.el-checkbox {
	vertical-align: middle;
}

.el-tree-node__content:hover {
	background: #e4e8f1;
}

.el-tree-node__expand-icon {
	margin-left: 10px;
	width: 0;
	height: 0;
	border: 6px solid transparent;
	cursor: pointer;
	transition: -webkit-transform .3s ease-in-out;
	transition: transform .3s ease-in-out;
	transition: transform .3s ease-in-out,-webkit-transform .3s ease-in-out;
	-webkit-transform: rotate(0);
	transform: rotate(0);
	border-right-width: 0;
	border-left-color: #97a8be;
	border-left-width: 7px;
}

.el-tree-node__expand-icon:hover {
	border-left-color: #999;
}

.el-tree-node__expand-icon.expanded {
	-webkit-transform: rotate(90deg);
	transform: rotate(90deg);
}

.el-tree-node__expand-icon.is-leaf {
	border-color: transparent;
	cursor: default;
}

.el-tree-node__label {
	font-size: 14px;
}

.el-tree-node__loading-icon {
	margin-right: 4px;
	color: #97a8be;
	font-size: 14px;
}

.el-tree--highlight-current .el-tree-node.is-current>.el-tree-node__content {
	background-color: #edf7ff;
}

.collapse-transition {
	transition: height .3s ease-in-out,padding-top .3s ease-in-out,padding-bottom .3s ease-in-out;
}

.el-menu-item,.el-submenu__title {
	position: relative;
	box-sizing: border-box;
	padding: 0 20px;
	height: 56px;
	color: #48576a;
	white-space: nowrap;
	font-size: 14px;
	line-height: 56px;
	cursor: pointer;
	transition: border-color .3s,background-color .3s,color .3s;
}

.el-menu {
	position: relative;
	margin: 0;
	padding-left: 0;
	border-radius: 2px;
	background-color: #eef1f6;
	list-style: none;
}

.el-menu:after,.el-menu:before {
	display: table;
}

.el-menu li {
	list-style: none;
}

.el-menu--dark {
	background-color: #324157;
}

.el-menu--dark .el-menu-item,.el-menu--dark .el-submenu__title {
	color: #bfcbd9;
}

.el-menu--dark .el-menu-item:hover,.el-menu--dark .el-submenu__title:hover {
	background-color: #48576a;
}

.el-menu--dark .el-submenu .el-menu {
	background-color: #1f2d3d;
}

.el-menu--dark .el-submenu .el-menu .el-menu-item:hover {
	background-color: #48576a;
}

.el-menu--horizontal .el-menu-item {
	position: relative;
	float: left;
	box-sizing: border-box;
	margin: 0;
	height: 60px;
	border-bottom: 5px solid transparent;
	line-height: 60px;
	cursor: pointer;
}

.el-menu--horizontal .el-menu-item a,.el-menu--horizontal .el-menu-item a:hover {
	color: inherit;
}

.el-menu--horizontal .el-submenu {
	position: relative;
	float: left;
}

.el-menu--horizontal .el-submenu>.el-menu {
	position: absolute;
	top: 65px;
	left: 0;
	z-index: 100;
	padding: 5px 0;
	min-width: 100%;
	border: 1px solid #d1dbe5;
	background-color: #fff;
}

.el-menu--horizontal .el-submenu .el-submenu__title {
	height: 60px;
	border-bottom: 5px solid transparent;
	line-height: 60px;
}

.el-menu--horizontal .el-submenu .el-menu-item {
	float: none;
	padding: 0 10px;
	height: 36px;
	background-color: #fff;
	line-height: 36px;
}

.el-menu--horizontal .el-submenu .el-submenu__icon-arrow {
	position: static;
	margin-top: -3px;
	margin-left: 5px;
	color: #97a8be;
	vertical-align: middle;
}

.el-menu--horizontal .el-menu-item:hover,.el-menu--horizontal .el-submenu__title:hover {
	background-color: #eef1f6;
}

.el-menu--horizontal>.el-menu-item:hover,.el-menu--horizontal>.el-submenu.is-active .el-submenu__title,.el-menu--horizontal>.el-submenu:hover .el-submenu__title {
	border-bottom: 5px solid #20a0ff;
}

.el-menu--horizontal.el-menu--dark .el-menu-item:hover,.el-menu--horizontal.el-menu--dark .el-submenu__title:hover {
	background-color: #324157;
}

.el-menu--horizontal.el-menu--dark .el-submenu .el-menu-item:hover,.el-menu--horizontal.el-menu--dark .el-submenu .el-submenu-title:hover,.el-menu-item:hover {
	background-color: #d1dbe5;
}

.el-menu--horizontal.el-menu--dark .el-submenu .el-menu-item,.el-menu--horizontal.el-menu--dark .el-submenu .el-submenu-title {
	color: #48576a;
}

.el-menu--horizontal.el-menu--dark .el-submenu .el-menu-item.is-active,.el-menu-item.is-active {
	color: #20a0ff;
}

.el-menu-item [class^=el-icon-] {
	margin-right: 10px;
	vertical-align: baseline;
}

.el-menu-item:first-child {
	margin-left: 0;
}

.el-menu-item:last-child {
	margin-right: 0;
}

.el-submenu [class^=el-icon-] {
	margin-right: 10px;
	vertical-align: baseline;
}

.el-submenu .el-menu {
	background-color: #e4e8f1;
}

.el-submenu .el-menu-item:hover,.el-submenu__title:hover {
	background-color: #d1dbe5;
}

.el-submenu .el-menu-item {
	padding: 0 45px;
	height: 50px;
	line-height: 50px;
}

.el-submenu.is-opened>.el-submenu__title .el-submenu__icon-arrow {
	-webkit-transform: rotate(180deg);
	transform: rotate(180deg);
}

.el-submenu.is-active .el-submenu__title {
	border-bottom-color: #20a0ff;
}

.el-submenu__title {
	position: relative;
}

.el-submenu__icon-arrow {
	position: absolute;
	top: 50%;
	right: 20px;
	margin-top: -7px;
	font-size: 12px;
	transition: -webkit-transform .3s;
	transition: transform .3s;
	transition: transform .3s,-webkit-transform .3s;
}

.el-menu-item-group>ul {
	padding: 0;
}

.el-menu-item-group__title {
	padding-top: 15px;
	padding-left: 20px;
	color: #97a8be;
	font-size: 14px;
	line-height: normal;
}

.el-progress {
	position: relative;
	line-height: 1;
}

.el-progress.is-exception .el-progress-bar__inner {
	background-color: #ff4949;
}

.el-progress.is-exception .el-progress__text {
	color: #ff4949;
}

.el-progress.is-success .el-progress-bar__inner {
	background-color: #13ce66;
}

.el-progress.is-success .el-progress__text {
	color: #13ce66;
}

.el-progress__text {
	display: inline-block;
	margin-left: 10px;
	color: #48576a;
	vertical-align: middle;
	font-size: 14px;
	line-height: 1;
}

.el-progress__text i {
	display: block;
	vertical-align: middle;
}

.el-progress--circle {
	display: inline-block;
}

.el-progress--circle .el-progress__text {
	position: absolute;
	top: 50%;
	left: 0;
	margin: 0;
	width: 100%;
	text-align: center;
	-webkit-transform: translateY(-50%);
	transform: translateY(-50%);
}

.el-progress--circle .el-progress__text i {
	display: inline-block;
	vertical-align: middle;
}

.el-progress--without-text .el-progress__text {
	display: none;
}

.el-progress--without-text .el-progress-bar {
	display: block;
	margin-right: 0;
	padding-right: 0;
}

.el-progress--text-inside .el-progress-bar {
	margin-right: 0;
	padding-right: 0;
}

.el-progress-bar {
	display: inline-block;
	box-sizing: border-box;
	margin-right: -55px;
	padding-right: 50px;
	width: 100%;
	vertical-align: middle;
}

.el-progress-bar__outer {
	position: relative;
	overflow: hidden;
	height: 6px;
	border-radius: 100px;
	background-color: #e4e8f1;
	vertical-align: middle;
}

.el-progress-bar__inner {
	position: absolute;
	top: 0;
	left: 0;
	height: 100%;
	border-radius: 100px;
	background-color: #20a0ff;
	text-align: right;
	line-height: 1;
}

.el-dragger,.el-dragger+.el-upload__tip {
	text-align: center;
}

.el-progress-bar__innerText {
	display: inline-block;
	margin: 0 5px;
	color: #fff;
	vertical-align: middle;
	font-size: 12px;
}

@-webkit-keyframes progress {
	0% {
		background-position: 0 0;
	}

	to {
		background-position: 32px 0;
	}
}

@keyframes progress {
	0% {
		background-position: 0 0;
	}

	to {
		background-position: 32px 0;
	}
}

.el-upload {
	width: 360px;
}

.el-upload__input {
	display: none;
}

.el-upload__inner {
	position: relative;
	display: inline-block;
}

.el-upload__inner iframe {
	position: absolute;
	top: 0;
	left: 0;
	z-index: -1;
	opacity: 0;
	filter: alpha(opacity=0);
}

.el-upload__files {
	margin: 0 0 10px;
	padding: 0;
	list-style: none;
}

.el-upload__file {
	position: relative;
	box-sizing: border-box;
	width: 100%;
	border-radius: 4px;
	color: #48576a;
	font-size: 14px;
	line-height: 32px;
	transition: all .5s cubic-bezier(.55,0,.1,1);
}

.el-upload__file a {
	display: block;
	overflow: hidden;
	margin-right: 40px;
	padding-left: 4px;
	color: #48576a;
	text-overflow: ellipsis;
	white-space: nowrap;
	transition: color .3s;
}

.el-upload__file a [class^=el-icon] {
	margin-right: 7px;
	height: 100%;
	color: #97a8be;
	line-height: inherit;
}

.el-upload__file .el-progress-bar {
	margin-right: 0;
	padding-right: 0;
}

.el-upload__file .el-progress {
	position: absolute;
	bottom: -3px;
	width: 100%;
}

.el-upload__file .el-progress__text {
	position: absolute;
	top: -10px;
	right: 0;
}

.el-upload__file:hover {
	background-color: #eef1f6;
}

.el-upload__file.is-finished a:hover {
	color: #20a0ff;
	cursor: pointer;
}

.el-upload__file.is-finished:hover .el-upload__btn-delete {
	display: block;
	cursor: pointer;
}

.el-upload__tip {
	margin-top: 7px;
	color: #8391a5;
	font-size: 12px;
}

.el-upload__btn-delete {
	position: absolute;
	top: 0;
	right: 15px;
	display: none;
	color: #20a0ff;
	font-size: 12px;
}

.el-dragger {
	position: relative;
	overflow: hidden;
	box-sizing: border-box;
	width: 360px;
	height: 180px;
	border: 1px solid #bfcbd9;
	border-radius: 4px;
	background-color: #fbfdff;
	cursor: pointer;
}

.el-dragger .el-upload__inner {
	display: block;
	height: 100%;
}

.el-dragger .el-icon-upload {
	margin: 40px 0 16px;
	color: #97a8be;
	font-size: 67px;
	line-height: 50px;
}

.el-dragger~.el-upload__files {
	margin-top: 7px;
	padding-top: 5px;
	border-top: 1px solid rgba(191,203,217,.2);
}

.el-dragger:not(.is-showCover):hover {
	border-color: #20a0ff;
}

.el-dragger.is-dragOver {
	border: 2px dashed #20a0ff;
	background-color: rgba(32,159,255,.06);
}

.el-dragger__cover {
	position: absolute;
	top: 0;
	left: 0;
	z-index: 10;
	overflow: hidden;
	width: 100%;
	height: 100%;
	cursor: default;
}

.el-dragger__cover:after {
	display: inline-block;
	height: 100%;
	vertical-align: middle;
}

.el-dragger__cover img {
	display: block;
	width: 100%;
	height: 100%;
}

.el-dragger__cover+.el-upload__inner {
	position: relative;
	z-index: 1;
	opacity: 0;
}

.el-dragger__cover__progress {
	position: static;
	display: inline-block;
	width: 243px;
	vertical-align: middle;
}

.el-dragger__cover__progress+.el-upload__inner {
	opacity: 0;
}

.el-dragger__cover__content {
	position: absolute;
	top: 0;
	left: 0;
	width: 100%;
	height: 100%;
}

.el-dragger__cover__interact {
	position: absolute;
	bottom: 0;
	left: 0;
	width: 100%;
	height: 100%;
	background-color: rgba(0,0,0,.72);
	text-align: center;
}

.el-dragger__cover__interact .btn {
	display: inline-block;
	margin-top: 60px;
	color: #fff;
	vertical-align: middle;
	font-size: 14px;
	cursor: pointer;
	transition: opacity .3s cubic-bezier(.23,1,.32,1) .1s,-webkit-transform .3s cubic-bezier(.23,1,.32,1) .1s;
	transition: transform .3s cubic-bezier(.23,1,.32,1) .1s,opacity .3s cubic-bezier(.23,1,.32,1) .1s;
	transition: transform .3s cubic-bezier(.23,1,.32,1) .1s,opacity .3s cubic-bezier(.23,1,.32,1) .1s,-webkit-transform .3s cubic-bezier(.23,1,.32,1) .1s;
}

.el-dragger__cover__interact .btn span {
	opacity: 0;
	transition: opacity .15s linear;
}

.el-dragger__cover__interact .btn:not(:first-child) {
	margin-left: 35px;
}

.el-dragger__cover__interact .btn:hover {
	-webkit-transform: translateY(-13px);
	transform: translateY(-13px);
}

.el-dragger__cover__interact .btn:hover span {
	opacity: 1;
}

.el-dragger__cover__interact .btn i {
	display: block;
	margin: 0 auto 5px;
	color: #fff;
	font-size: 24px;
	line-height: inherit;
}

.el-dragger__cover__title {
	position: absolute;
	bottom: 0;
	left: 0;
	overflow: hidden;
	margin: 0;
	padding: 0 10px;
	width: 100%;
	height: 36px;
	background-color: #fff;
	color: #48576a;
	text-align: left;
	text-overflow: ellipsis;
	white-space: nowrap;
	font-weight: 400;
	font-size: 14px;
	line-height: 36px;
}

.el-badge,.el-col-pull-1,.el-col-pull-2,.el-col-pull-3,.el-col-pull-4,.el-col-pull-5,.el-col-pull-6,.el-col-pull-7,.el-col-pull-8,.el-col-pull-9,.el-col-pull-10,.el-col-pull-11,.el-col-pull-12,.el-col-pull-13,.el-col-pull-14,.el-col-pull-15,.el-col-pull-16,.el-col-pull-17,.el-col-pull-18,.el-col-pull-19,.el-col-pull-20,.el-col-pull-21,.el-col-pull-22,.el-col-pull-23,.el-col-pull-24,.el-col-push-1,.el-col-push-2,.el-col-push-3,.el-col-push-4,.el-col-push-5,.el-col-push-6,.el-col-push-7,.el-col-push-8,.el-col-push-9,.el-col-push-10,.el-col-push-11,.el-col-push-13,.el-col-push-14,.el-col-push-15,.el-col-push-16,.el-col-push-17,.el-col-push-18,.el-col-push-19,.el-col-push-20,.el-col-push-21,.el-col-push-22,.el-col-push-23,.el-col-push-24,.el-row {
	position: relative;
}

.el-dragger__text,.el-step__head,.el-steps.is-horizontal.is-center {
	text-align: center;
}

.el-dragger__text {
	color: #97a8be;
	font-size: 14px;
}

.el-dragger__text em {
	color: #20a0ff;
	font-style: normal;
}

.el-row {
	box-sizing: border-box;
}

.el-row:after,.el-row:before {
	display: table;
}

.el-row--flex {
	display: -ms-flexbox;
	display: -webkit-box;
	display: flex;
}

.el-row--flex:after,.el-row--flex:before {
	display: none;
}

.el-badge,.el-badge__content,.el-spinner {
	display: inline-block;
}

.el-row--flex.is-align-bottom {
	-ms-flex-align: end;
	-webkit-box-align: end;
	align-items: flex-end;
}

.el-row--flex.is-align-middle {
	-ms-flex-align: center;
	-webkit-box-align: center;
	align-items: center;
}

.el-row--flex.is-justify-space-around {
	-ms-flex-pack: distribute;
	justify-content: space-around;
}

.el-row--flex.is-justify-space-between {
	-ms-flex-pack: justify;
	-webkit-box-pack: justify;
	justify-content: space-between;
}

.el-row--flex.is-justify-end {
	-ms-flex-pack: end;
	-webkit-box-pack: end;
	justify-content: flex-end;
}

.el-row--flex.is-justify-center {
	-ms-flex-pack: center;
	-webkit-box-pack: center;
	justify-content: center;
}

.el-col-1,.el-col-2,.el-col-3,.el-col-4,.el-col-5,.el-col-6,.el-col-7,.el-col-8,.el-col-9,.el-col-10,.el-col-11,.el-col-12,.el-col-13,.el-col-14,.el-col-15,.el-col-16,.el-col-17,.el-col-18,.el-col-19,.el-col-20,.el-col-21,.el-col-22,.el-col-23,.el-col-24 {
	float: left;
	box-sizing: border-box;
}

.el-col-1 {
	width: 4.16667%;
}

.el-col-offset-1 {
	margin-left: 4.16667%;
}

.el-col-pull-1 {
	right: 4.16667%;
}

.el-col-push-1 {
	left: 4.16667%;
}

.el-col-2 {
	width: 8.33333%;
}

.el-col-offset-2 {
	margin-left: 8.33333%;
}

.el-col-pull-2 {
	right: 8.33333%;
}

.el-col-push-2 {
	left: 8.33333%;
}

.el-col-3 {
	width: 12.5%;
}

.el-col-offset-3 {
	margin-left: 12.5%;
}

.el-col-pull-3 {
	right: 12.5%;
}

.el-col-push-3 {
	left: 12.5%;
}

.el-col-4 {
	width: 16.66667%;
}

.el-col-offset-4 {
	margin-left: 16.66667%;
}

.el-col-pull-4 {
	right: 16.66667%;
}

.el-col-push-4 {
	left: 16.66667%;
}

.el-col-5 {
	width: 20.83333%;
}

.el-col-offset-5 {
	margin-left: 20.83333%;
}

.el-col-pull-5 {
	right: 20.83333%;
}

.el-col-push-5 {
	left: 20.83333%;
}

.el-col-6 {
	width: 25%;
}

.el-col-offset-6 {
	margin-left: 25%;
}

.el-col-pull-6 {
	right: 25%;
}

.el-col-push-6 {
	left: 25%;
}

.el-col-7 {
	width: 29.16667%;
}

.el-col-offset-7 {
	margin-left: 29.16667%;
}

.el-col-pull-7 {
	right: 29.16667%;
}

.el-col-push-7 {
	left: 29.16667%;
}

.el-col-8 {
	width: 33.33333%;
}

.el-col-offset-8 {
	margin-left: 33.33333%;
}

.el-col-pull-8 {
	right: 33.33333%;
}

.el-col-push-8 {
	left: 33.33333%;
}

.el-col-9 {
	width: 37.5%;
}

.el-col-offset-9 {
	margin-left: 37.5%;
}

.el-col-pull-9 {
	right: 37.5%;
}

.el-col-push-9 {
	left: 37.5%;
}

.el-col-10 {
	width: 41.66667%;
}

.el-col-offset-10 {
	margin-left: 41.66667%;
}

.el-col-pull-10 {
	right: 41.66667%;
}

.el-col-push-10 {
	left: 41.66667%;
}

.el-col-11 {
	width: 45.83333%;
}

.el-col-offset-11 {
	margin-left: 45.83333%;
}

.el-col-pull-11 {
	right: 45.83333%;
}

.el-col-push-11 {
	left: 45.83333%;
}

.el-col-12 {
	width: 50%;
}

.el-col-offset-12 {
	margin-left: 50%;
}

.el-col-pull-12 {
	right: 50%;
}

.el-col-push-12 {
	position: relative;
	left: 50%;
}

.el-col-13 {
	width: 54.16667%;
}

.el-col-offset-13 {
	margin-left: 54.16667%;
}

.el-col-pull-13 {
	right: 54.16667%;
}

.el-col-push-13 {
	left: 54.16667%;
}

.el-col-14 {
	width: 58.33333%;
}

.el-col-offset-14 {
	margin-left: 58.33333%;
}

.el-col-pull-14 {
	right: 58.33333%;
}

.el-col-push-14 {
	left: 58.33333%;
}

.el-col-15 {
	width: 62.5%;
}

.el-col-offset-15 {
	margin-left: 62.5%;
}

.el-col-pull-15 {
	right: 62.5%;
}

.el-col-push-15 {
	left: 62.5%;
}

.el-col-16 {
	width: 66.66667%;
}

.el-col-offset-16 {
	margin-left: 66.66667%;
}

.el-col-pull-16 {
	right: 66.66667%;
}

.el-col-push-16 {
	left: 66.66667%;
}

.el-col-17 {
	width: 70.83333%;
}

.el-col-offset-17 {
	margin-left: 70.83333%;
}

.el-col-pull-17 {
	right: 70.83333%;
}

.el-col-push-17 {
	left: 70.83333%;
}

.el-col-18 {
	width: 75%;
}

.el-col-offset-18 {
	margin-left: 75%;
}

.el-col-pull-18 {
	right: 75%;
}

.el-col-push-18 {
	left: 75%;
}

.el-col-19 {
	width: 79.16667%;
}

.el-col-offset-19 {
	margin-left: 79.16667%;
}

.el-col-pull-19 {
	right: 79.16667%;
}

.el-col-push-19 {
	left: 79.16667%;
}

.el-col-20 {
	width: 83.33333%;
}

.el-col-offset-20 {
	margin-left: 83.33333%;
}

.el-col-pull-20 {
	right: 83.33333%;
}

.el-col-push-20 {
	left: 83.33333%;
}

.el-col-21 {
	width: 87.5%;
}

.el-col-offset-21 {
	margin-left: 87.5%;
}

.el-col-pull-21 {
	right: 87.5%;
}

.el-col-push-21 {
	left: 87.5%;
}

.el-col-22 {
	width: 91.66667%;
}

.el-col-offset-22 {
	margin-left: 91.66667%;
}

.el-col-pull-22 {
	right: 91.66667%;
}

.el-col-push-22 {
	left: 91.66667%;
}

.el-col-23 {
	width: 95.83333%;
}

.el-col-offset-23 {
	margin-left: 95.83333%;
}

.el-col-pull-23 {
	right: 95.83333%;
}

.el-col-push-23 {
	left: 95.83333%;
}

.el-col-24 {
	width: 100%;
}

.el-col-offset-24 {
	margin-left: 100%;
}

.el-col-pull-24 {
	right: 100%;
}

.el-col-push-24 {
	left: 100%;
}

@media (max-width:768px) {
	.el-col-xs-1 {
		width: 4.16667%;
	}

	.el-col-xs-offset-1 {
		margin-left: 4.16667%;
	}

	.el-col-xs-pull-1 {
		position: relative;
		right: 4.16667%;
	}

	.el-col-xs-push-1 {
		position: relative;
		left: 4.16667%;
	}

	.el-col-xs-2 {
		width: 8.33333%;
	}

	.el-col-xs-offset-2 {
		margin-left: 8.33333%;
	}

	.el-col-xs-pull-2 {
		position: relative;
		right: 8.33333%;
	}

	.el-col-xs-push-2 {
		position: relative;
		left: 8.33333%;
	}

	.el-col-xs-3 {
		width: 12.5%;
	}

	.el-col-xs-offset-3 {
		margin-left: 12.5%;
	}

	.el-col-xs-pull-3 {
		position: relative;
		right: 12.5%;
	}

	.el-col-xs-push-3 {
		position: relative;
		left: 12.5%;
	}

	.el-col-xs-4 {
		width: 16.66667%;
	}

	.el-col-xs-offset-4 {
		margin-left: 16.66667%;
	}

	.el-col-xs-pull-4 {
		position: relative;
		right: 16.66667%;
	}

	.el-col-xs-push-4 {
		position: relative;
		left: 16.66667%;
	}

	.el-col-xs-5 {
		width: 20.83333%;
	}

	.el-col-xs-offset-5 {
		margin-left: 20.83333%;
	}

	.el-col-xs-pull-5 {
		position: relative;
		right: 20.83333%;
	}

	.el-col-xs-push-5 {
		position: relative;
		left: 20.83333%;
	}

	.el-col-xs-6 {
		width: 25%;
	}

	.el-col-xs-offset-6 {
		margin-left: 25%;
	}

	.el-col-xs-pull-6 {
		position: relative;
		right: 25%;
	}

	.el-col-xs-push-6 {
		position: relative;
		left: 25%;
	}

	.el-col-xs-7 {
		width: 29.16667%;
	}

	.el-col-xs-offset-7 {
		margin-left: 29.16667%;
	}

	.el-col-xs-pull-7 {
		position: relative;
		right: 29.16667%;
	}

	.el-col-xs-push-7 {
		position: relative;
		left: 29.16667%;
	}

	.el-col-xs-8 {
		width: 33.33333%;
	}

	.el-col-xs-offset-8 {
		margin-left: 33.33333%;
	}

	.el-col-xs-pull-8 {
		position: relative;
		right: 33.33333%;
	}

	.el-col-xs-push-8 {
		position: relative;
		left: 33.33333%;
	}

	.el-col-xs-9 {
		width: 37.5%;
	}

	.el-col-xs-offset-9 {
		margin-left: 37.5%;
	}

	.el-col-xs-pull-9 {
		position: relative;
		right: 37.5%;
	}

	.el-col-xs-push-9 {
		position: relative;
		left: 37.5%;
	}

	.el-col-xs-10 {
		width: 41.66667%;
	}

	.el-col-xs-offset-10 {
		margin-left: 41.66667%;
	}

	.el-col-xs-pull-10 {
		position: relative;
		right: 41.66667%;
	}

	.el-col-xs-push-10 {
		position: relative;
		left: 41.66667%;
	}

	.el-col-xs-11 {
		width: 45.83333%;
	}

	.el-col-xs-offset-11 {
		margin-left: 45.83333%;
	}

	.el-col-xs-pull-11 {
		position: relative;
		right: 45.83333%;
	}

	.el-col-xs-push-11 {
		position: relative;
		left: 45.83333%;
	}

	.el-col-xs-12 {
		width: 50%;
	}

	.el-col-xs-offset-12 {
		margin-left: 50%;
	}

	.el-col-xs-pull-12 {
		position: relative;
		right: 50%;
	}

	.el-col-xs-push-12 {
		position: relative;
		left: 50%;
	}

	.el-col-xs-13 {
		width: 54.16667%;
	}

	.el-col-xs-offset-13 {
		margin-left: 54.16667%;
	}

	.el-col-xs-pull-13 {
		position: relative;
		right: 54.16667%;
	}

	.el-col-xs-push-13 {
		position: relative;
		left: 54.16667%;
	}

	.el-col-xs-14 {
		width: 58.33333%;
	}

	.el-col-xs-offset-14 {
		margin-left: 58.33333%;
	}

	.el-col-xs-pull-14 {
		position: relative;
		right: 58.33333%;
	}

	.el-col-xs-push-14 {
		position: relative;
		left: 58.33333%;
	}

	.el-col-xs-15 {
		width: 62.5%;
	}

	.el-col-xs-offset-15 {
		margin-left: 62.5%;
	}

	.el-col-xs-pull-15 {
		position: relative;
		right: 62.5%;
	}

	.el-col-xs-push-15 {
		position: relative;
		left: 62.5%;
	}

	.el-col-xs-16 {
		width: 66.66667%;
	}

	.el-col-xs-offset-16 {
		margin-left: 66.66667%;
	}

	.el-col-xs-pull-16 {
		position: relative;
		right: 66.66667%;
	}

	.el-col-xs-push-16 {
		position: relative;
		left: 66.66667%;
	}

	.el-col-xs-17 {
		width: 70.83333%;
	}

	.el-col-xs-offset-17 {
		margin-left: 70.83333%;
	}

	.el-col-xs-pull-17 {
		position: relative;
		right: 70.83333%;
	}

	.el-col-xs-push-17 {
		position: relative;
		left: 70.83333%;
	}

	.el-col-xs-18 {
		width: 75%;
	}

	.el-col-xs-offset-18 {
		margin-left: 75%;
	}

	.el-col-xs-pull-18 {
		position: relative;
		right: 75%;
	}

	.el-col-xs-push-18 {
		position: relative;
		left: 75%;
	}

	.el-col-xs-19 {
		width: 79.16667%;
	}

	.el-col-xs-offset-19 {
		margin-left: 79.16667%;
	}

	.el-col-xs-pull-19 {
		position: relative;
		right: 79.16667%;
	}

	.el-col-xs-push-19 {
		position: relative;
		left: 79.16667%;
	}

	.el-col-xs-20 {
		width: 83.33333%;
	}

	.el-col-xs-offset-20 {
		margin-left: 83.33333%;
	}

	.el-col-xs-pull-20 {
		position: relative;
		right: 83.33333%;
	}

	.el-col-xs-push-20 {
		position: relative;
		left: 83.33333%;
	}

	.el-col-xs-21 {
		width: 87.5%;
	}

	.el-col-xs-offset-21 {
		margin-left: 87.5%;
	}

	.el-col-xs-pull-21 {
		position: relative;
		right: 87.5%;
	}

	.el-col-xs-push-21 {
		position: relative;
		left: 87.5%;
	}

	.el-col-xs-22 {
		width: 91.66667%;
	}

	.el-col-xs-offset-22 {
		margin-left: 91.66667%;
	}

	.el-col-xs-pull-22 {
		position: relative;
		right: 91.66667%;
	}

	.el-col-xs-push-22 {
		position: relative;
		left: 91.66667%;
	}

	.el-col-xs-23 {
		width: 95.83333%;
	}

	.el-col-xs-offset-23 {
		margin-left: 95.83333%;
	}

	.el-col-xs-pull-23 {
		position: relative;
		right: 95.83333%;
	}

	.el-col-xs-push-23 {
		position: relative;
		left: 95.83333%;
	}

	.el-col-xs-24 {
		width: 100%;
	}

	.el-col-xs-offset-24 {
		margin-left: 100%;
	}

	.el-col-xs-pull-24 {
		position: relative;
		right: 100%;
	}

	.el-col-xs-push-24 {
		position: relative;
		left: 100%;
	}
}

@media (min-width:768px) {
	.el-col-sm-1 {
		width: 4.16667%;
	}

	.el-col-sm-offset-1 {
		margin-left: 4.16667%;
	}

	.el-col-sm-pull-1 {
		position: relative;
		right: 4.16667%;
	}

	.el-col-sm-push-1 {
		position: relative;
		left: 4.16667%;
	}

	.el-col-sm-2 {
		width: 8.33333%;
	}

	.el-col-sm-offset-2 {
		margin-left: 8.33333%;
	}

	.el-col-sm-pull-2 {
		position: relative;
		right: 8.33333%;
	}

	.el-col-sm-push-2 {
		position: relative;
		left: 8.33333%;
	}

	.el-col-sm-3 {
		width: 12.5%;
	}

	.el-col-sm-offset-3 {
		margin-left: 12.5%;
	}

	.el-col-sm-pull-3 {
		position: relative;
		right: 12.5%;
	}

	.el-col-sm-push-3 {
		position: relative;
		left: 12.5%;
	}

	.el-col-sm-4 {
		width: 16.66667%;
	}

	.el-col-sm-offset-4 {
		margin-left: 16.66667%;
	}

	.el-col-sm-pull-4 {
		position: relative;
		right: 16.66667%;
	}

	.el-col-sm-push-4 {
		position: relative;
		left: 16.66667%;
	}

	.el-col-sm-5 {
		width: 20.83333%;
	}

	.el-col-sm-offset-5 {
		margin-left: 20.83333%;
	}

	.el-col-sm-pull-5 {
		position: relative;
		right: 20.83333%;
	}

	.el-col-sm-push-5 {
		position: relative;
		left: 20.83333%;
	}

	.el-col-sm-6 {
		width: 25%;
	}

	.el-col-sm-offset-6 {
		margin-left: 25%;
	}

	.el-col-sm-pull-6 {
		position: relative;
		right: 25%;
	}

	.el-col-sm-push-6 {
		position: relative;
		left: 25%;
	}

	.el-col-sm-7 {
		width: 29.16667%;
	}

	.el-col-sm-offset-7 {
		margin-left: 29.16667%;
	}

	.el-col-sm-pull-7 {
		position: relative;
		right: 29.16667%;
	}

	.el-col-sm-push-7 {
		position: relative;
		left: 29.16667%;
	}

	.el-col-sm-8 {
		width: 33.33333%;
	}

	.el-col-sm-offset-8 {
		margin-left: 33.33333%;
	}

	.el-col-sm-pull-8 {
		position: relative;
		right: 33.33333%;
	}

	.el-col-sm-push-8 {
		position: relative;
		left: 33.33333%;
	}

	.el-col-sm-9 {
		width: 37.5%;
	}

	.el-col-sm-offset-9 {
		margin-left: 37.5%;
	}

	.el-col-sm-pull-9 {
		position: relative;
		right: 37.5%;
	}

	.el-col-sm-push-9 {
		position: relative;
		left: 37.5%;
	}

	.el-col-sm-10 {
		width: 41.66667%;
	}

	.el-col-sm-offset-10 {
		margin-left: 41.66667%;
	}

	.el-col-sm-pull-10 {
		position: relative;
		right: 41.66667%;
	}

	.el-col-sm-push-10 {
		position: relative;
		left: 41.66667%;
	}

	.el-col-sm-11 {
		width: 45.83333%;
	}

	.el-col-sm-offset-11 {
		margin-left: 45.83333%;
	}

	.el-col-sm-pull-11 {
		position: relative;
		right: 45.83333%;
	}

	.el-col-sm-push-11 {
		position: relative;
		left: 45.83333%;
	}

	.el-col-sm-12 {
		width: 50%;
	}

	.el-col-sm-offset-12 {
		margin-left: 50%;
	}

	.el-col-sm-pull-12 {
		position: relative;
		right: 50%;
	}

	.el-col-sm-push-12 {
		position: relative;
		left: 50%;
	}

	.el-col-sm-13 {
		width: 54.16667%;
	}

	.el-col-sm-offset-13 {
		margin-left: 54.16667%;
	}

	.el-col-sm-pull-13 {
		position: relative;
		right: 54.16667%;
	}

	.el-col-sm-push-13 {
		position: relative;
		left: 54.16667%;
	}

	.el-col-sm-14 {
		width: 58.33333%;
	}

	.el-col-sm-offset-14 {
		margin-left: 58.33333%;
	}

	.el-col-sm-pull-14 {
		position: relative;
		right: 58.33333%;
	}

	.el-col-sm-push-14 {
		position: relative;
		left: 58.33333%;
	}

	.el-col-sm-15 {
		width: 62.5%;
	}

	.el-col-sm-offset-15 {
		margin-left: 62.5%;
	}

	.el-col-sm-pull-15 {
		position: relative;
		right: 62.5%;
	}

	.el-col-sm-push-15 {
		position: relative;
		left: 62.5%;
	}

	.el-col-sm-16 {
		width: 66.66667%;
	}

	.el-col-sm-offset-16 {
		margin-left: 66.66667%;
	}

	.el-col-sm-pull-16 {
		position: relative;
		right: 66.66667%;
	}

	.el-col-sm-push-16 {
		position: relative;
		left: 66.66667%;
	}

	.el-col-sm-17 {
		width: 70.83333%;
	}

	.el-col-sm-offset-17 {
		margin-left: 70.83333%;
	}

	.el-col-sm-pull-17 {
		position: relative;
		right: 70.83333%;
	}

	.el-col-sm-push-17 {
		position: relative;
		left: 70.83333%;
	}

	.el-col-sm-18 {
		width: 75%;
	}

	.el-col-sm-offset-18 {
		margin-left: 75%;
	}

	.el-col-sm-pull-18 {
		position: relative;
		right: 75%;
	}

	.el-col-sm-push-18 {
		position: relative;
		left: 75%;
	}

	.el-col-sm-19 {
		width: 79.16667%;
	}

	.el-col-sm-offset-19 {
		margin-left: 79.16667%;
	}

	.el-col-sm-pull-19 {
		position: relative;
		right: 79.16667%;
	}

	.el-col-sm-push-19 {
		position: relative;
		left: 79.16667%;
	}

	.el-col-sm-20 {
		width: 83.33333%;
	}

	.el-col-sm-offset-20 {
		margin-left: 83.33333%;
	}

	.el-col-sm-pull-20 {
		position: relative;
		right: 83.33333%;
	}

	.el-col-sm-push-20 {
		position: relative;
		left: 83.33333%;
	}

	.el-col-sm-21 {
		width: 87.5%;
	}

	.el-col-sm-offset-21 {
		margin-left: 87.5%;
	}

	.el-col-sm-pull-21 {
		position: relative;
		right: 87.5%;
	}

	.el-col-sm-push-21 {
		position: relative;
		left: 87.5%;
	}

	.el-col-sm-22 {
		width: 91.66667%;
	}

	.el-col-sm-offset-22 {
		margin-left: 91.66667%;
	}

	.el-col-sm-pull-22 {
		position: relative;
		right: 91.66667%;
	}

	.el-col-sm-push-22 {
		position: relative;
		left: 91.66667%;
	}

	.el-col-sm-23 {
		width: 95.83333%;
	}

	.el-col-sm-offset-23 {
		margin-left: 95.83333%;
	}

	.el-col-sm-pull-23 {
		position: relative;
		right: 95.83333%;
	}

	.el-col-sm-push-23 {
		position: relative;
		left: 95.83333%;
	}

	.el-col-sm-24 {
		width: 100%;
	}

	.el-col-sm-offset-24 {
		margin-left: 100%;
	}

	.el-col-sm-pull-24 {
		position: relative;
		right: 100%;
	}

	.el-col-sm-push-24 {
		position: relative;
		left: 100%;
	}
}

@media (min-width:992px) {
	.el-col-md-1 {
		width: 4.16667%;
	}

	.el-col-md-offset-1 {
		margin-left: 4.16667%;
	}

	.el-col-md-pull-1 {
		position: relative;
		right: 4.16667%;
	}

	.el-col-md-push-1 {
		position: relative;
		left: 4.16667%;
	}

	.el-col-md-2 {
		width: 8.33333%;
	}

	.el-col-md-offset-2 {
		margin-left: 8.33333%;
	}

	.el-col-md-pull-2 {
		position: relative;
		right: 8.33333%;
	}

	.el-col-md-push-2 {
		position: relative;
		left: 8.33333%;
	}

	.el-col-md-3 {
		width: 12.5%;
	}

	.el-col-md-offset-3 {
		margin-left: 12.5%;
	}

	.el-col-md-pull-3 {
		position: relative;
		right: 12.5%;
	}

	.el-col-md-push-3 {
		position: relative;
		left: 12.5%;
	}

	.el-col-md-4 {
		width: 16.66667%;
	}

	.el-col-md-offset-4 {
		margin-left: 16.66667%;
	}

	.el-col-md-pull-4 {
		position: relative;
		right: 16.66667%;
	}

	.el-col-md-push-4 {
		position: relative;
		left: 16.66667%;
	}

	.el-col-md-5 {
		width: 20.83333%;
	}

	.el-col-md-offset-5 {
		margin-left: 20.83333%;
	}

	.el-col-md-pull-5 {
		position: relative;
		right: 20.83333%;
	}

	.el-col-md-push-5 {
		position: relative;
		left: 20.83333%;
	}

	.el-col-md-6 {
		width: 25%;
	}

	.el-col-md-offset-6 {
		margin-left: 25%;
	}

	.el-col-md-pull-6 {
		position: relative;
		right: 25%;
	}

	.el-col-md-push-6 {
		position: relative;
		left: 25%;
	}

	.el-col-md-7 {
		width: 29.16667%;
	}

	.el-col-md-offset-7 {
		margin-left: 29.16667%;
	}

	.el-col-md-pull-7 {
		position: relative;
		right: 29.16667%;
	}

	.el-col-md-push-7 {
		position: relative;
		left: 29.16667%;
	}

	.el-col-md-8 {
		width: 33.33333%;
	}

	.el-col-md-offset-8 {
		margin-left: 33.33333%;
	}

	.el-col-md-pull-8 {
		position: relative;
		right: 33.33333%;
	}

	.el-col-md-push-8 {
		position: relative;
		left: 33.33333%;
	}

	.el-col-md-9 {
		width: 37.5%;
	}

	.el-col-md-offset-9 {
		margin-left: 37.5%;
	}

	.el-col-md-pull-9 {
		position: relative;
		right: 37.5%;
	}

	.el-col-md-push-9 {
		position: relative;
		left: 37.5%;
	}

	.el-col-md-10 {
		width: 41.66667%;
	}

	.el-col-md-offset-10 {
		margin-left: 41.66667%;
	}

	.el-col-md-pull-10 {
		position: relative;
		right: 41.66667%;
	}

	.el-col-md-push-10 {
		position: relative;
		left: 41.66667%;
	}

	.el-col-md-11 {
		width: 45.83333%;
	}

	.el-col-md-offset-11 {
		margin-left: 45.83333%;
	}

	.el-col-md-pull-11 {
		position: relative;
		right: 45.83333%;
	}

	.el-col-md-push-11 {
		position: relative;
		left: 45.83333%;
	}

	.el-col-md-12 {
		width: 50%;
	}

	.el-col-md-offset-12 {
		margin-left: 50%;
	}

	.el-col-md-pull-12 {
		position: relative;
		right: 50%;
	}

	.el-col-md-push-12 {
		position: relative;
		left: 50%;
	}

	.el-col-md-13 {
		width: 54.16667%;
	}

	.el-col-md-offset-13 {
		margin-left: 54.16667%;
	}

	.el-col-md-pull-13 {
		position: relative;
		right: 54.16667%;
	}

	.el-col-md-push-13 {
		position: relative;
		left: 54.16667%;
	}

	.el-col-md-14 {
		width: 58.33333%;
	}

	.el-col-md-offset-14 {
		margin-left: 58.33333%;
	}

	.el-col-md-pull-14 {
		position: relative;
		right: 58.33333%;
	}

	.el-col-md-push-14 {
		position: relative;
		left: 58.33333%;
	}

	.el-col-md-15 {
		width: 62.5%;
	}

	.el-col-md-offset-15 {
		margin-left: 62.5%;
	}

	.el-col-md-pull-15 {
		position: relative;
		right: 62.5%;
	}

	.el-col-md-push-15 {
		position: relative;
		left: 62.5%;
	}

	.el-col-md-16 {
		width: 66.66667%;
	}

	.el-col-md-offset-16 {
		margin-left: 66.66667%;
	}

	.el-col-md-pull-16 {
		position: relative;
		right: 66.66667%;
	}

	.el-col-md-push-16 {
		position: relative;
		left: 66.66667%;
	}

	.el-col-md-17 {
		width: 70.83333%;
	}

	.el-col-md-offset-17 {
		margin-left: 70.83333%;
	}

	.el-col-md-pull-17 {
		position: relative;
		right: 70.83333%;
	}

	.el-col-md-push-17 {
		position: relative;
		left: 70.83333%;
	}

	.el-col-md-18 {
		width: 75%;
	}

	.el-col-md-offset-18 {
		margin-left: 75%;
	}

	.el-col-md-pull-18 {
		position: relative;
		right: 75%;
	}

	.el-col-md-push-18 {
		position: relative;
		left: 75%;
	}

	.el-col-md-19 {
		width: 79.16667%;
	}

	.el-col-md-offset-19 {
		margin-left: 79.16667%;
	}

	.el-col-md-pull-19 {
		position: relative;
		right: 79.16667%;
	}

	.el-col-md-push-19 {
		position: relative;
		left: 79.16667%;
	}

	.el-col-md-20 {
		width: 83.33333%;
	}

	.el-col-md-offset-20 {
		margin-left: 83.33333%;
	}

	.el-col-md-pull-20 {
		position: relative;
		right: 83.33333%;
	}

	.el-col-md-push-20 {
		position: relative;
		left: 83.33333%;
	}

	.el-col-md-21 {
		width: 87.5%;
	}

	.el-col-md-offset-21 {
		margin-left: 87.5%;
	}

	.el-col-md-pull-21 {
		position: relative;
		right: 87.5%;
	}

	.el-col-md-push-21 {
		position: relative;
		left: 87.5%;
	}

	.el-col-md-22 {
		width: 91.66667%;
	}

	.el-col-md-offset-22 {
		margin-left: 91.66667%;
	}

	.el-col-md-pull-22 {
		position: relative;
		right: 91.66667%;
	}

	.el-col-md-push-22 {
		position: relative;
		left: 91.66667%;
	}

	.el-col-md-23 {
		width: 95.83333%;
	}

	.el-col-md-offset-23 {
		margin-left: 95.83333%;
	}

	.el-col-md-pull-23 {
		position: relative;
		right: 95.83333%;
	}

	.el-col-md-push-23 {
		position: relative;
		left: 95.83333%;
	}

	.el-col-md-24 {
		width: 100%;
	}

	.el-col-md-offset-24 {
		margin-left: 100%;
	}

	.el-col-md-pull-24 {
		position: relative;
		right: 100%;
	}

	.el-col-md-push-24 {
		position: relative;
		left: 100%;
	}
}

@media (min-width:1200px) {
	.el-col-lg-1 {
		width: 4.16667%;
	}

	.el-col-lg-offset-1 {
		margin-left: 4.16667%;
	}

	.el-col-lg-pull-1 {
		position: relative;
		right: 4.16667%;
	}

	.el-col-lg-push-1 {
		position: relative;
		left: 4.16667%;
	}

	.el-col-lg-2 {
		width: 8.33333%;
	}

	.el-col-lg-offset-2 {
		margin-left: 8.33333%;
	}

	.el-col-lg-pull-2 {
		position: relative;
		right: 8.33333%;
	}

	.el-col-lg-push-2 {
		position: relative;
		left: 8.33333%;
	}

	.el-col-lg-3 {
		width: 12.5%;
	}

	.el-col-lg-offset-3 {
		margin-left: 12.5%;
	}

	.el-col-lg-pull-3 {
		position: relative;
		right: 12.5%;
	}

	.el-col-lg-push-3 {
		position: relative;
		left: 12.5%;
	}

	.el-col-lg-4 {
		width: 16.66667%;
	}

	.el-col-lg-offset-4 {
		margin-left: 16.66667%;
	}

	.el-col-lg-pull-4 {
		position: relative;
		right: 16.66667%;
	}

	.el-col-lg-push-4 {
		position: relative;
		left: 16.66667%;
	}

	.el-col-lg-5 {
		width: 20.83333%;
	}

	.el-col-lg-offset-5 {
		margin-left: 20.83333%;
	}

	.el-col-lg-pull-5 {
		position: relative;
		right: 20.83333%;
	}

	.el-col-lg-push-5 {
		position: relative;
		left: 20.83333%;
	}

	.el-col-lg-6 {
		width: 25%;
	}

	.el-col-lg-offset-6 {
		margin-left: 25%;
	}

	.el-col-lg-pull-6 {
		position: relative;
		right: 25%;
	}

	.el-col-lg-push-6 {
		position: relative;
		left: 25%;
	}

	.el-col-lg-7 {
		width: 29.16667%;
	}

	.el-col-lg-offset-7 {
		margin-left: 29.16667%;
	}

	.el-col-lg-pull-7 {
		position: relative;
		right: 29.16667%;
	}

	.el-col-lg-push-7 {
		position: relative;
		left: 29.16667%;
	}

	.el-col-lg-8 {
		width: 33.33333%;
	}

	.el-col-lg-offset-8 {
		margin-left: 33.33333%;
	}

	.el-col-lg-pull-8 {
		position: relative;
		right: 33.33333%;
	}

	.el-col-lg-push-8 {
		position: relative;
		left: 33.33333%;
	}

	.el-col-lg-9 {
		width: 37.5%;
	}

	.el-col-lg-offset-9 {
		margin-left: 37.5%;
	}

	.el-col-lg-pull-9 {
		position: relative;
		right: 37.5%;
	}

	.el-col-lg-push-9 {
		position: relative;
		left: 37.5%;
	}

	.el-col-lg-10 {
		width: 41.66667%;
	}

	.el-col-lg-offset-10 {
		margin-left: 41.66667%;
	}

	.el-col-lg-pull-10 {
		position: relative;
		right: 41.66667%;
	}

	.el-col-lg-push-10 {
		position: relative;
		left: 41.66667%;
	}

	.el-col-lg-11 {
		width: 45.83333%;
	}

	.el-col-lg-offset-11 {
		margin-left: 45.83333%;
	}

	.el-col-lg-pull-11 {
		position: relative;
		right: 45.83333%;
	}

	.el-col-lg-push-11 {
		position: relative;
		left: 45.83333%;
	}

	.el-col-lg-12 {
		width: 50%;
	}

	.el-col-lg-offset-12 {
		margin-left: 50%;
	}

	.el-col-lg-pull-12 {
		position: relative;
		right: 50%;
	}

	.el-col-lg-push-12 {
		position: relative;
		left: 50%;
	}

	.el-col-lg-13 {
		width: 54.16667%;
	}

	.el-col-lg-offset-13 {
		margin-left: 54.16667%;
	}

	.el-col-lg-pull-13 {
		position: relative;
		right: 54.16667%;
	}

	.el-col-lg-push-13 {
		position: relative;
		left: 54.16667%;
	}

	.el-col-lg-14 {
		width: 58.33333%;
	}

	.el-col-lg-offset-14 {
		margin-left: 58.33333%;
	}

	.el-col-lg-pull-14 {
		position: relative;
		right: 58.33333%;
	}

	.el-col-lg-push-14 {
		position: relative;
		left: 58.33333%;
	}

	.el-col-lg-15 {
		width: 62.5%;
	}

	.el-col-lg-offset-15 {
		margin-left: 62.5%;
	}

	.el-col-lg-pull-15 {
		position: relative;
		right: 62.5%;
	}

	.el-col-lg-push-15 {
		position: relative;
		left: 62.5%;
	}

	.el-col-lg-16 {
		width: 66.66667%;
	}

	.el-col-lg-offset-16 {
		margin-left: 66.66667%;
	}

	.el-col-lg-pull-16 {
		position: relative;
		right: 66.66667%;
	}

	.el-col-lg-push-16 {
		position: relative;
		left: 66.66667%;
	}

	.el-col-lg-17 {
		width: 70.83333%;
	}

	.el-col-lg-offset-17 {
		margin-left: 70.83333%;
	}

	.el-col-lg-pull-17 {
		position: relative;
		right: 70.83333%;
	}

	.el-col-lg-push-17 {
		position: relative;
		left: 70.83333%;
	}

	.el-col-lg-18 {
		width: 75%;
	}

	.el-col-lg-offset-18 {
		margin-left: 75%;
	}

	.el-col-lg-pull-18 {
		position: relative;
		right: 75%;
	}

	.el-col-lg-push-18 {
		position: relative;
		left: 75%;
	}

	.el-col-lg-19 {
		width: 79.16667%;
	}

	.el-col-lg-offset-19 {
		margin-left: 79.16667%;
	}

	.el-col-lg-pull-19 {
		position: relative;
		right: 79.16667%;
	}

	.el-col-lg-push-19 {
		position: relative;
		left: 79.16667%;
	}

	.el-col-lg-20 {
		width: 83.33333%;
	}

	.el-col-lg-offset-20 {
		margin-left: 83.33333%;
	}

	.el-col-lg-pull-20 {
		position: relative;
		right: 83.33333%;
	}

	.el-col-lg-push-20 {
		position: relative;
		left: 83.33333%;
	}

	.el-col-lg-21 {
		width: 87.5%;
	}

	.el-col-lg-offset-21 {
		margin-left: 87.5%;
	}

	.el-col-lg-pull-21 {
		position: relative;
		right: 87.5%;
	}

	.el-col-lg-push-21 {
		position: relative;
		left: 87.5%;
	}

	.el-col-lg-22 {
		width: 91.66667%;
	}

	.el-col-lg-offset-22 {
		margin-left: 91.66667%;
	}

	.el-col-lg-pull-22 {
		position: relative;
		right: 91.66667%;
	}

	.el-col-lg-push-22 {
		position: relative;
		left: 91.66667%;
	}

	.el-col-lg-23 {
		width: 95.83333%;
	}

	.el-col-lg-offset-23 {
		margin-left: 95.83333%;
	}

	.el-col-lg-pull-23 {
		position: relative;
		right: 95.83333%;
	}

	.el-col-lg-push-23 {
		position: relative;
		left: 95.83333%;
	}

	.el-col-lg-24 {
		width: 100%;
	}

	.el-col-lg-offset-24 {
		margin-left: 100%;
	}

	.el-col-lg-pull-24 {
		position: relative;
		right: 100%;
	}

	.el-col-lg-push-24 {
		position: relative;
		left: 100%;
	}
}

.el-time-spinner {
	width: 100%;
	white-space: nowrap;
}

.el-spinner {
	vertical-align: middle;
}

.el-spinner-inner {
	width: 50px;
	height: 50px;
	-webkit-animation: rotate 2s linear infinite;
	animation: rotate 2s linear infinite;
}

.el-spinner-inner .path {
	stroke: #ececec;
	stroke-linecap: round;
	-webkit-animation: dash 1.5s ease-in-out infinite;
	animation: dash 1.5s ease-in-out infinite;
}

@-webkit-keyframes rotate {
	to {
		-webkit-transform: rotate(1turn);
		transform: rotate(1turn);
	}
}

@keyframes rotate {
	to {
		-webkit-transform: rotate(1turn);
		transform: rotate(1turn);
	}
}

@-webkit-keyframes dash {
	0% {
		stroke-dasharray: 1,150;
		stroke-dashoffset: 0;
	}

	50% {
		stroke-dasharray: 90,150;
		stroke-dashoffset: -35;
	}

	to {
		stroke-dasharray: 90,150;
		stroke-dashoffset: -124;
	}
}

@keyframes dash {
	0% {
		stroke-dasharray: 1,150;
		stroke-dashoffset: 0;
	}

	50% {
		stroke-dasharray: 90,150;
		stroke-dashoffset: -35;
	}

	to {
		stroke-dasharray: 90,150;
		stroke-dashoffset: -124;
	}
}

.el-badge {
	vertical-align: middle;
}

.el-badge__content {
	padding: 0 6px;
	height: 18px;
	border: 1px solid #fff;
	border-radius: 10px;
	background-color: #ff4949;
	color: #fff;
	text-align: center;
	white-space: nowrap;
	font-size: 12px;
	line-height: 18px;
}

.el-badge__content.is-dot {
	right: 0;
	padding: 0;
	width: 8px;
	height: 8px;
	border-radius: 50%;
}

.el-badge__content.is-fixed {
	position: absolute;
	top: 0;
	right: 10px;
	-webkit-transform: translateY(-50%) translateX(100%);
	transform: translateY(-50%) translateX(100%);
}

.el-rate__icon,.el-rate__item {
	position: relative;
	display: inline-block;
}

.el-badge__content.is-fixed.is-dot {
	right: 5px;
}

.el-card {
	overflow: hidden;
	border: 1px solid #d1dbe5;
	border-radius: 4px;
	background-color: #fff;
}

.el-card__header {
	box-sizing: border-box;
	padding: 18px 20px;
	border-bottom: 1px solid #d1dbe5;
}

.el-card__body {
	padding: 20px;
}

.el-rate {
	height: 20px;
	line-height: 1;
}

.el-rate__item {
	vertical-align: middle;
	font-size: 0;
}

.el-rate__icon {
	margin-right: 6px;
	color: #bfcbd9;
	font-size: 18px;
	transition: .3s;
}

.el-rate__decimal,.el-rate__icon .path2 {
	position: absolute;
	top: 0;
	left: 0;
}

.el-rate__icon.hover {
	-webkit-transform: scale(1.15);
	transform: scale(1.15);
}

.el-rate__decimal {
	display: inline-block;
	overflow: hidden;
}

.el-rate__text {
	vertical-align: middle;
	font-size: 14px;
}

.el-steps {
	font-size: 0;
}

.el-steps>:last-child .el-step__line {
	display: none;
}

.el-step.is-horizontal,.el-step.is-vertical .el-step__head,.el-step.is-vertical .el-step__main,.el-step__line {
	display: inline-block;
}

.el-steps.is-horizontal {
	white-space: nowrap;
}

.el-step {
	position: relative;
	vertical-align: top;
}

.el-step.is-vertical .el-step__main {
	padding-left: 10px;
}

.el-step__line {
	position: absolute;
	border-color: inherit;
	background-color: #bfcbd9;
}

.el-step__line.is-vertical {
	top: 32px;
	bottom: 0;
	left: 15px;
	box-sizing: border-box;
	width: 2px;
}

.el-step__line.is-horizontal {
	top: 15px;
	right: 0;
	left: 32px;
	height: 2px;
}

.el-step__line.is-icon.is-horizontal {
	right: 4px;
}

.el-step__line-inner {
	display: block;
	width: 0;
	height: 0;
	border-color: inherit;
	border-style: solid;
	border-width: 1px;
	transition: all .15s;
}

.el-step__icon {
	display: block;
	line-height: 28px;
}

.el-step__icon>* {
	vertical-align: middle;
	line-height: inherit;
}

.el-step__head {
	width: 28px;
	height: 28px;
	border-radius: 50%;
	background-color: transparent;
	vertical-align: top;
	font-size: 28px;
	line-height: 28px;
	transition: all .15s;
}

.el-step__head.is-finish {
	border-color: #20a0ff;
	color: #20a0ff;
}

.el-step__head.is-error {
	border-color: #ff4949;
	color: #ff4949;
}

.el-step__head.is-success {
	border-color: #13ce66;
	color: #13ce66;
}

.el-step__head.is-process,.el-step__head.is-wait {
	border-color: #bfcbd9;
	color: #bfcbd9;
}

.el-step__head.is-text {
	border-style: solid;
	border-width: 2px;
	font-size: 14px;
}

.el-step__head.is-text.is-finish {
	border-color: #20a0ff;
	background-color: #20a0ff;
	color: #fff;
}

.el-step__head.is-text.is-error {
	border-color: #ff4949;
	background-color: #ff4949;
	color: #fff;
}

.el-step__head.is-text.is-success {
	border-color: #13ce66;
	background-color: #13ce66;
	color: #fff;
}

.el-step__head.is-text.is-wait {
	border-color: #bfcbd9;
	background-color: #fff;
	color: #bfcbd9;
}

.el-step__head.is-text.is-process {
	border-color: #bfcbd9;
	background-color: #bfcbd9;
	color: #fff;
}

.el-step__main {
	padding-right: 10px;
	text-align: left;
	white-space: normal;
}

.el-step__title {
	display: inline-block;
	font-size: 14px;
	line-height: 32px;
}

.el-step__title.is-finish {
	color: #20a0ff;
	font-weight: 700;
}

.el-step__title.is-error {
	color: #ff4949;
	font-weight: 700;
}

.el-step__title.is-success {
	color: #13ce66;
	font-weight: 700;
}

.el-step__title.is-wait {
	color: #97a8be;
	font-weight: 400;
}

.el-step__title.is-process {
	color: #48576a;
	font-weight: 700;
}

.el-step__description {
	font-weight: 400;
	font-size: 12px;
	line-height: 14px;
}

.el-step__description.is-finish {
	color: #20a0ff;
}

.el-step__description.is-error {
	color: #ff4949;
}

.el-step__description.is-success {
	color: #13ce66;
}

.el-step__description.is-wait {
	color: #bfcbd9;
}

.el-step__description.is-process {
	color: #8391a5;
}

.el-scrollbar {
	position: relative;
	overflow: hidden;
}

.el-scrollbar:active .el-scrollbar__bar,.el-scrollbar:focus .el-scrollbar__bar,.el-scrollbar:hover .el-scrollbar__bar {
	opacity: 1;
	transition: opacity .34s ease-out;
}

.el-scrollbar__wrap {
	overflow: scroll;
}

.el-scrollbar__wrap--hidden-default::-webkit-scrollbar {
	width: 0;
	height: 0;
}

.el-scrollbar__thumb {
	position: relative;
	display: block;
	width: 0;
	height: 0;
	border-radius: inherit;
	background-color: rgba(151,168,190,.3);
	cursor: pointer;
	transition: background-color .3s;
}

.el-scrollbar__thumb:hover {
	background-color: rgba(151,168,190,.5);
}

.el-scrollbar__bar {
	position: absolute;
	right: 2px;
	bottom: 2px;
	z-index: 1;
	border-radius: 4px;
	opacity: 0;
	transition: opacity .12s ease-out;
}

.el-carousel__arrow,.el-carousel__button {
	margin: 0;
	outline: 0;
	cursor: pointer;
	transition: .3s;
}

.el-scrollbar__bar.is-horizontal {
	left: 2px;
	height: 6px;
}

.el-scrollbar__bar.is-horizontal>div {
	height: 100%;
}

.el-scrollbar__bar.is-vertical {
	top: 2px;
	width: 6px;
}

.el-scrollbar__bar.is-vertical>div {
	width: 100%;
}

.el-carousel {
	position: relative;
	overflow-x: hidden;
}

.el-carousel__container {
	position: relative;
	height: 300px;
}

.el-carousel__arrow {
	position: absolute;
	top: 50%;
	z-index: 10;
	padding: 0;
	width: 36px;
	height: 36px;
	border: none;
	border-radius: 50%;
	background-color: rgba(31,45,61,.11);
	color: #fff;
	text-align: center;
	font-size: 12px;
	-webkit-transform: translateY(-50%);
	transform: translateY(-50%);
}

.el-carousel__arrow:hover {
	background-color: rgba(31,45,61,.23);
}

.el-carousel__arrow i {
	cursor: pointer;
}

.el-carousel__arrow--left {
	left: 16px;
}

.el-carousel__arrow--right {
	right: 16px;
}

.el-carousel__indicators {
	position: absolute;
	bottom: 0;
	left: 50%;
	margin: 0;
	padding: 0;
	list-style: none;
	-webkit-transform: translateX(-50%);
	transform: translateX(-50%);
}

.el-carousel__indicators--outside {
	position: static;
	bottom: 26px;
	text-align: center;
	-webkit-transform: none;
	transform: none;
}

.el-carousel__indicators--outside .el-carousel__indicator:hover button {
	opacity: .64;
}

.el-carousel__indicators--outside button {
	background-color: #8391a5;
	opacity: .24;
}

.el-carousel__indicator {
	display: inline-block;
	padding: 12px 4px;
	background-color: transparent;
	cursor: pointer;
}

.el-carousel__indicator:hover button {
	opacity: .72;
}

.el-carousel__indicator.is-active button {
	opacity: 1;
}

.el-carousel__button {
	display: block;
	padding: 0;
	width: 30px;
	height: 2px;
	border: none;
	background-color: #fff;
	opacity: .48;
}

.el-carousel__item,.el-carousel__mask {
	position: absolute;
	top: 0;
	left: 0;
	height: 100%;
}

.carousel-arrow-left-enter,.carousel-arrow-left-leave-active {
	opacity: 0;
	-webkit-transform: translateY(-50%) translateX(-10px);
	transform: translateY(-50%) translateX(-10px);
}

.carousel-arrow-right-enter,.carousel-arrow-right-leave-active {
	opacity: 0;
	-webkit-transform: translateY(-50%) translateX(10px);
	transform: translateY(-50%) translateX(10px);
}

.el-carousel__item {
	display: inline-block;
	overflow: hidden;
	width: 100%;
	transition: .4s ease-in-out;
}

.el-carousel__item--card {
	z-index: 0;
	width: 50%;
}

.el-carousel__item--card.is-in-stage {
	z-index: 1;
	cursor: pointer;
}

.el-carousel__item--card.is-in-stage.is-hover .el-carousel__mask,.el-carousel__item--card.is-in-stage:hover .el-carousel__mask {
	opacity: .12;
}

.el-carousel__item--card.is-active {
	z-index: 2;
}

.el-carousel__mask {
	width: 100%;
	background-color: #fff;
	opacity: .24;
	transition: .2s;
}

.el-collapse {
	border: 1px solid #dfe6ec;
	border-radius: 0;
}

.el-collapse-item:last-child {
	margin-bottom: -1px;
}

.el-collapse-item.is-active .el-collapse-item__header__arrow {
	-webkit-transform: rotate(90deg);
	transform: rotate(90deg);
}

.el-collapse-item__header {
	padding-left: 15px;
	height: 43px;
	border-bottom: 1px solid #dfe6ec;
	background-color: #fff;
	color: #48576a;
	font-size: 13px;
	line-height: 43px;
	cursor: pointer;
}

.el-collapse-item__header__arrow {
	margin-right: 8px;
	transition: -webkit-transform .3s;
	transition: transform .3s;
	transition: transform .3s,-webkit-transform .3s;
}

.el-collapse-item__wrap {
	overflow: hidden;
	box-sizing: border-box;
	border-bottom: 1px solid #dfe6ec;
	background-color: #fbfdff;
	transition: height .3s cubic-bezier(.215,.61,.355,1);
	will-change: height;
}

.el-collapse-item__content {
	padding: 10px 15px;
	color: #1f2d3d;
	font-size: 13px;
	line-height: 1.769230769230769;
}

.header-operations:after,header:after {
	display: inline-block;
	height: 100%;
	content: "";
	vertical-align: middle;
}

html {
	font-family: sans-serif;
	-ms-text-size-adjust: 100%;
	-webkit-text-size-adjust: 100%;
}

body {
	margin: 0;
}

article,aside,details,figcaption,figure,footer,header,main,menu,nav,section,summary {
	display: block;
}

audio,canvas,progress,video {
	display: inline-block;
}

audio:not([controls]) {
	display: none;
	height: 0;
}

progress {
	vertical-align: baseline;
}[hidden],template {
	display: none;
}

a {
	background-color: transparent;
	-webkit-text-decoration-skip: objects;
}

a:active,a:hover {
	outline-width: 0;
}

abbr[title] {
	border-bottom: none;
	text-decoration: underline;
	text-decoration: underline dotted;
}

b,strong {
	font-weight: inherit;
	font-weight: bolder;
}

dfn {
	font-style: italic;
}

h1 {
	margin: .67em 0;
	font-size: 2em;
}

mark {
	background-color: #ff0;
	color: #000;
}

small {
	font-size: 80%;
}

sub,sup {
	position: relative;
	vertical-align: baseline;
	font-size: 75%;
	line-height: 0;
}

sub {
	bottom: -.25em;
}

sup {
	top: -.5em;
}

img {
	border-style: none;
}

svg:not(:root) {
	overflow: hidden;
}

code,kbd,pre,samp {
	font-size: 1em;
	font-family: monospace,monospace;
}

figure {
	margin: 1em 40px;
}

hr {
	overflow: visible;
	box-sizing: content-box;
	height: 0;
}

button,input,optgroup,select,textarea {
	margin: 0;
	color: inherit;
	vertical-align: middle;
	font: inherit;
}

optgroup {
	font-weight: 700;
}

button,input {
	overflow: visible;
}

button,select {
	text-transform: none;
}[type=reset],[type=submit],button,html [type=button] {
	-webkit-appearance: button;
}[type=button]::-moz-focus-inner,[type=reset]::-moz-focus-inner,[type=submit]::-moz-focus-inner,button::-moz-focus-inner {
	padding: 0;
	border-style: none;
}[type=button]:-moz-focusring,[type=reset]:-moz-focusring,[type=submit]:-moz-focusring,button:-moz-focusring {
	outline: 1px dotted ButtonText;
}

fieldset {
	margin: 0 2px;
	padding: .35em .625em .75em;
	border: 1px solid silver;
}

legend {
	display: table;
	box-sizing: border-box;
	padding: 0;
	max-width: 100%;
	color: inherit;
	white-space: normal;
}

textarea {
	overflow: auto;
	vertical-align: top;
	resize: none;
}

input,select,textarea {
	outline: 0;
}[disabled] {
	cursor: default;
}[type=checkbox],[type=radio] {
	box-sizing: border-box;
	padding: 0;
}[type=number]::-webkit-inner-spin-button,[type=number]::-webkit-outer-spin-button {
	height: auto;
}[type=search] {
	outline-offset: -2px;
	-webkit-appearance: textfield;
}[type=search]::-webkit-search-cancel-button,[type=search]::-webkit-search-decoration {
	-webkit-appearance: none;
}

input::-moz-placeholder,textarea::-moz-placeholder {
	color: inherit;
	opacity: .54;
}

input:-ms-input-placeholder,textarea:-ms-input-placeholder {
	color: inherit;
	opacity: .54;
}

input::-webkit-input-placeholder,textarea::-webkit-input-placeholder {
	color: inherit;
	opacity: .54;
}

input::-ms-clear,input::-ms-reveal {
	display: none;
}

::-webkit-file-upload-button {
	font: inherit;
	-webkit-appearance: button;
}

table {
	border-collapse: collapse;
	border-spacing: 0;
}

td,th {
	padding: 0;
}

blockquote,figure,form,h1,h2,h3,h4,h5,h6,p {
	margin: 0;
}

dd,dl,li,ol,ul {
	margin: 0;
	padding: 0;
}

ol,ul {
	list-style: none outside none;
}

h1,h2,h3,h4,h5,h6 {
	font-weight: 400;
	font-size: 100%;
}

.el-menu,body,html {
	height: 100%;
}

body {
	font-family: Helvetica Neue,Helvetica,PingFang SC,Hiragino Sans GB,Microsoft YaHei,SimSun,sans-serif;
}

a {
	text-decoration: none;
}

header {
	position: absolute;
	top: 0;
	left: 0;
	z-index: 1;
	box-sizing: border-box;
	padding: 0 20px;
	width: 100%;
	height: 80px;
}

.container,.wrapper {
	height: 100%;
}

.wrapper {
	position: relative;
}

.container {
	padding-top: 80px;
}

.menu {
	height: 100%;
}

.content {
	padding: 25px;
}

.header-logo {
	display: inline-block;
  vertical-align: middle;
  width: 16.67%
}

.header-operations {
	float: right;
	display: inline-block;
	padding-right: 30px;
	height: 100%;
}

.header-operations li {
	display: inline-block;
	margin: 0 10px;
	padding: 0 10px;
	color: #fff;
	vertical-align: middle;
	line-height: 80px;
	cursor: pointer;
}

.header-operations li:last-child {
	cursor: default;
}

.header-operations .header-download {
	opacity: .4;
	cursor: default;
}

.header-operations .header-download.is-available {
	opacity: 1;
	cursor: pointer;
}

.header-operations span {
	opacity: .7;
}

.header-operations .header-lang {
	cursor: pointer;
}

.header-operations .header-lang.is-active {
	opacity: 1;
}

.color-buttons {
	float: right;
}

.query-form {
	margin-top: 20px;
	padding-top: 25px;
	background-color: #f2f2f2;
}

.table {
	margin-top: 30px;
}

.pagination {
	float: right;
	margin-top: 10px;
}

.help p {
	margin-bottom: 10px;
}

.help p a {
	color: #20a0ff;
}
</style>

