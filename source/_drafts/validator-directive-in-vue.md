---
title: vue的自定义校验指令
tags:
  - vue
---

自定义validator指令，支持默认规则，选项规则和自定义规则。
默认规则为空值检查。
选项规则则可以指定可为空，必须为数字。
自定义规则可以是数字大小限制，字符串长度, 自定义校验函数，自定义正则表达式。

下面是指令代码：
```javascript
// 默认规则： 非空
// 选项规则：可为空，数字，
// 自定义规则：数字大小限制，字符串长度, 自定义校验函数，自定义正则表达式

// 检测非空
function checkEmpty(value) {
  return !value.trim();
}
// 检查是否是数字
function checkNumber(value) {
  if (value === '' || value === null) {
    return false;
  }
  if (isNaN(Number(value))) {
    return false;
  } else {
    return true;
  }
}
// 检测数字区间
function checkLimit(value, rule) {
  if (!checkNumber(value)) {
    return false;
  }
  if (rule.min !== undefined && value < rule.min) {
    return false;
  }
  if (rule.max !== undefined && value > rule.max) {
    return false;
  }
  return true;
}
// 检测字符长度
function checkCharLength(value, rule) {
  let length = value.length;
  if (rule.min !== undefined && length < rule.min) {
    return false;
  }
  if (rule.max !== undefined && length > rule.max) {
    return false;
  }
  return true;
}
// 检测正则
function checkReg(value, rule) {
  return rule.checkReg.test(value);
}

// 创建校验器
function createValidator (vm) {
  if (!vm.$validator) {
    vm.$validator = {
      validateList: [],
      errorList: [],
      checkAll() {
        this.validateList.forEach(validate => validate());
        let _result = true;
        this.errorList.forEach(item => {
          _result = _result && item.value;
        })
        return _result;
      }
    };
  }
  return vm.$validator;
}


export default {
  bind: function (el, binding, vnode) {
    let vm = vnode.context; // 当前的vue实例
    let $validator = createValidator(vm);
    let option = binding.modifiers; // 选项规则
    let customRule = binding.value; // 自定义规则
    // let value = el.value; // 输入框的值

    function validate () {
      let value = el.value;
      // 同步信息到vm.$validator中
      let _index = $validator.validateList.indexOf(validate);
      $validator.errorList[_index] = {value: true, messageList: []}; // 重置
      let result = $validator.errorList[_index];
      // 检查选项规则
      if (checkEmpty(value)) {
        // 如果可以为空，且实际数值就是空，则直接返回。不做下面的判断
        if (option.empty) {
          return;
        } else {
          result.value = false;
          result.messageList.push('不能为空');
        }
      }
      if (option.number && !checkNumber(value)) {
        result.value = false;
        result.messageList.push('必须为数字');
      }
      // 自定义规则
      if (customRule && customRule instanceof Array) {
        customRule.forEach(rule => {
          // 默认type是string
          if (rule.type === 'number') {
            // 数字大小限制
            if ((rule.min !== undefined || rule.max !== undefined) && !checkLimit(value, rule)) {
              result.value = false;
              result.messageList.push(rule.message || '数值不在范围内');
            }
          } else if (rule.type === 'regexp' && !checkReg(value, rule)) {
            // 自定义正则表达式
            result.value = false;
            result.messageList.push(rule.message || '不符合正则表达式');
          } else {
            // 字符串长度
            if ((rule.min !== undefined || rule.max !== undefined) && !checkCharLength(value, rule)) {
              result.value = false;
              result.messageList.push(rule.message || '长度不在范围内');
            }
          }
          // 自定义校验函数
          if (rule.validator && typeof rule.validator === 'function') {
            if (!rule.validator(value, rule)) {
              result.value = false;
              result.messageList.push(rule.message || '未通过检验');
            }
          }
        });
      }
      // 添加和移除相应的错误样式
      if (!result.value) {
        el.classList.add('hy-error');
      } else {
        el.classList.remove('hy-error');
      }
      return result;
    }
    $validator.validateList.push(validate);
    $validator.errorList.push({}); // 仅仅是为了占位，重置将会在validate里面做
    // 在blur上都绑定了处理事件
    el.addEventListener('blur', validate)
  }
};
```
