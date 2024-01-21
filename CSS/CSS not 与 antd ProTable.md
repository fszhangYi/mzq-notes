
# 如何使用 CSS `:not` 选择器进行选择性样式设置

CSS 提供了一个强大的伪类选择器 `:not()`，它允许您排除某些元素，使其不被选择。当您想对广泛的元素应用样式，同时排除基于其类、id 或属性的某些特定元素时，此选择器非常有用。

## 理解 `:not` 选择器

`:not()` 选择器针对所有不符合给定参数的元素。它就像是告诉浏览器：“对所有元素应用这些样式，除了这些。”

## 基本语法

`:not` 选择器的语法非常直接：

```css
element:not(argument) {
  /* 样式 */
}
```

这里的 `element` 是您想要样式化的 HTML 元素，`argument` 是不应该接收样式的元素的条件。

## 使用 `:not` 选择器的分步指南

### 步骤 1：确定目标元素

首先，确定您想要样式化的元素。例如，假设您有多个带有 `ant-col` 和 `ant-col-6` 类的 `<div>` 元素，您希望对它们全部应用样式，除了那些也有 `ant-col-offset-18` 类的元素。

### 步骤 2：应用 `:not` 选择器

要将样式应用于所有 `ant-col ant-col-6` 元素，除了带有 `ant-col-offset-18` 类的元素，您可以这样写：

```css
.ant-col.ant-col-6:not(.ant-col-offset-18) {
  /* 样式 */
}
```

### 步骤 3：编写 CSS 规则

现在，包括您想要应用的属性和值。例如，要设置右填充为 50px，规则将是：

```css
.ant-col.ant-col-6:not(.ant-col-offset-18) {
  padding-right: 50px;
}
```

### 步骤 4：将规则添加到样式表

将此规则包括在您的 CSS 文件中的适当选择器范围内。如果它是在 `form` 中使用的，您将像这样嵌套它：

```css
form {
  .ant-col.ant-col-6:not(.ant-col-offset-18) {
    padding-right: 50px;
  }
}
```

注意，如果您使用的是 SCSS 这样的 CSS 预处理器，嵌套将按所示工作。在纯 CSS 中，您不会嵌套规则。

### 步骤 5：覆盖其他样式

如果有其他样式规则与您想要应用的规则冲突，并且您需要确保您的 `:not` 规则优先，您可以使用 `!important` 声明：

```css
.ant-col.ant-col-6:not(.ant-col-offset-18) {
  padding-right: 50px !important;
}
```

然而，要谨慎使用 `!important`，因为它会破坏样式的自然级联流，使调试和维护变得更加困难。

### 步骤 6：测试您的样式

在添加 CSS 规则后，在浏览器中测试它以确保样式按预期应用。您应该看到除了 `ant-col-offset-18` 类的 `ant-col ant-col-6` 元素外，所有这些元素的右填充都设置为 50px。

## 结论

`:not` 选择器是一个多功能工具，可帮助创建更具体和有针对性的样式规则，而不会影响不应该接收某些样式的元素。按照上述步骤，您可以有效地使用 `:not` 选择器来完善您网

页的设计，确保不同元素之间有一致的外观和感觉。

---

# English version

# How to Use the CSS `:not` Selector for Selective Styling

CSS provides a powerful pseudo-class selector called `:not()` that allows you to exclude certain elements from being selected. This selector can be extremely useful when you want to apply a style to a broad range of elements while excluding some specific elements based on their class, id, or attributes.

## Understanding the `:not` Selector

The `:not()` selector targets all elements that do not match the given argument. It's like saying to the browser, "Apply these styles to all elements except for these."

## Basic Syntax

The syntax for the `:not` selector is straightforward:

```css
element:not(argument) {
  /* styles */
}
```

Here, `element` is the HTML element you want to style, and `argument` is the condition for elements that should not receive the style.

## Step-by-Step Guide to Using `:not` Selector

### Step 1: Determine the Target Elements

First, identify the elements you want to style. For example, let's say you have multiple `<div>` elements with the class `ant-col` and `ant-col-6`, and you want to apply styles to all of them except those that also have the class `ant-col-offset-18`.

### Step 2: Apply the `:not` Selector

To apply a style to all `ant-col ant-col-6` elements except those with the `ant-col-offset-18` class, you would write:

```css
.ant-col.ant-col-6:not(.ant-col-offset-18) {
  /* styles */
}
```

### Step 3: Write Your CSS Rule

Now, include the property and value you want to apply. For instance, to set the right padding to 50px, the rule would be:

```css
.ant-col.ant-col-6:not(.ant-col-offset-18) {
  padding-right: 50px;
}
```

### Step 4: Add the Rule to Your Stylesheet

Include this rule in your CSS file within the appropriate selector scope. If it's meant to be applied within a `form`, you would nest it like so:

```css
form {
  .ant-col.ant-col-6:not(.ant-col-offset-18) {
    padding-right: 50px;
  }
}
```

Note that if you're using a CSS preprocessor like SCSS, the nesting will work as shown. In plain CSS, you would not nest the rules.

### Step 5: Overriding Other Styles

If there are other style rules that conflict with what you want to apply, and you need to ensure that your `:not` rule takes precedence, you can use the `!important` declaration:

```css
.ant-col.ant-col-6:not(.ant-col-offset-18) {
  padding-right: 50px !important;
}
```

However, use `!important` sparingly, as it can make debugging and maintenance more difficult by breaking the natural cascading flow of styles.

### Step 6: Test Your Styles

After adding your CSS rule, test it in the browser to ensure that the styles are applied as expected. You should see the right padding of 50px applied to all `ant-col ant-col-6` elements except those with `ant-col-offset-18`.

## Conclusion

The `:not` selector is a versatile tool that helps in creating more specific and targeted style rules without affecting elements that should not receive certain styles. By following the steps above, you can effectively use the `:not` selector to refine your webpage's design and ensure a consistent look and feel across different elements.
