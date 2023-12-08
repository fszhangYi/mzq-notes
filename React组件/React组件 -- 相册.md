# 封装一个相册组件

在摄影的世界里，每一张照片都是独一无二的故事，而相册则是这些故事的沉默叙述者。随着互联网技术的发展，数字化相册已成为人们珍藏美好记忆的新方式。于是，在技术的洪流之中，承载着记忆的相册组件静静诞生。

## 背景和需求分析

在现代Web应用程序中，上传、展示照片已成为一项基础功能。为了提升用户体验，需要一个自动化的解决方案来处理图片上传、预览、删除和设为封面等操作。该组件应当能够验证文件类型和大小，同时支持自定义样式和操作，以适应不同的业务场景。

## 技术选型与框架搭建

本文将采用`React`作为构建组件的框架，以其组件化、高效和灵活的特性，实现一个功能齐全的相册组件。结合著名的`antd`设计语言，提高开发效率并确保用户界面的一致性和美观。

## 设计组件API

相册组件接收如下属性:

- `value`：控制组件当前显示的图片列表。
- `accept`：定义允许上传的图片格式列表，默认支持`.bmp`、`.jpg`、`.jpeg`、`.png`、`.webp`等常见图片格式。
- `action`：指定上传的服务器端地址。
- `maxSize`：限定图片的最大文件大小，默认为2MB。
- `tips`：为用户提供操作提示信息。
- `onChange`：当图片列表发生变化时的回调函数。
- `maxCount`：限制可上传图片的最大数量。

## 实现文件列表管理逻辑

组件内部通过`useState`勾子维护图片列表，并通过`triggerChange`函数在合适的时机通知外界状态的变化。以此确保父组件可以随时获取到最新的图片列表。

## 定义交云处理函数

### 图片上传逻辑

`handleUpload`函数负责处理图片的上传操作，当用户选择文件后，该函数会创建`FormData`并通过`extendRequest`将文件发送到指定的服务器端。上传成功后，将图片信息加入至图片列表中并触发`onChange`。

### 图片大小验证

`beforeUpload`函数被设计用来验证用户上传的图片大小是否在限定范围内。如果超出界限，则弹出提示信息并取消上传。

### 处理图片的移除和设为封面

通过`handleRemove`和`handleMarkCover`两个函数，分别实现了移除图片和设为封面的功能。这里涉及到数组的过滤、映射等操作，展现了JavaScript数组操作的灵活性。

## 营造交云界面

### 自定义图片项渲染

借助于`itemRender`函数，可以对相册中的每个图片项进行定制化的渲染处理。在这里，通过结合`Image`组件和`Row`布局，对图片的预览与操作区域进行美观而实用的设计。

### 上传按钮的呈现

通过全局定义的`uploadButton`，提供了一个直观的上传入口。当上传数量达到上限时，该按钮会自动隐藏，确保用户不会超出设定的最大上传数目。

## 组件的最终归纳

整个相册组件被包装在`React`函数组件`FormUploadAlbum`中，它提供了`Upload`组件的封装，并在内部处理了所有的业务逻辑。它不仅仅是一个单一的功能模块，更是一场交云和美学的完美结合。

## 最终代码
```js
import React, { useState } from 'react';
import { message, Upload, Image, Space, Row, Col } from 'antd';
// request是封装起来的，用于发送网络请求的对象
import { request } from '../../utils';
import {
  PlusOutlined,
  StarOutlined,
  DeleteOutlined,
  StarFilled,
} from '@ant-design/icons';
import './index.less';

// 允许上传的文件格式
const formatList = ['.bmp', '.jpg', '.jpeg', '.png', '.webp'].join(',');

// 新增上传的按钮
const uploadButton = (
  <div>
    <PlusOutlined />
    <div className="ant-upload-text">Upload</div>
  </div>
);

// 封装好的组件UploadAlbum
export default function UploadAlbum(props) {
  const {
    value = [], // 相册数组
    accept = formatList, // 允许上传的图片格式
    action = '', // 接口地址
    maxSize = 1024 * 2, // 文件最大size俄日默认2MB
    tips = 'Recommanded file size is less than 2MB, keep visual elements centered', // 提示信息
    onChange, // 相册数组发生改变的时候的回调
    maxCount, // 允许最大的上传数目
  } = props;

  // 封装组件内部应对相册数组变化的回调；在其中会执行外部对应的回调onChange方法
  const triggerChange = fileList => {
    onChange && onChange(fileList || []);
  };

  // 新增相片导致相册数组的值发生变化之后的回调函数
  const handleUpload = async info => {
    try {
      // 从antd组件中提取信息，构造网络通信的payload
      const { file } = info;
      const formData = new FormData();
      formData.append('file', file);
      // 将新增相片之后的相册数据同步到后端
      const result = await request.post(action, {
        data: formData,
      });
      const { success, msg, data = {} } = result;
      // 如果回填成功，则调用相册数据改变的回调函数
      if (success) {
        const { imagePath, thumbnailPpath, ...others } = data;
        triggerChange([
          ...value,
          {
            uid: `${imagePath}${new Date()}`,
            status: 'done',
            url: imagePath,
            oriPicUrl: imagePath,
            compressPicUrl: thumbnailPpath,
            isCover: value.length ? 0 : 1,
            ...others,
          },
        ]);
      } else {
        message.error(msg);
      }
    } catch (e) {
      message.error(e.message);
    }
  };

  // 相册新增相片的拦截器 -- 在这里做上传文件的大小校验
  const beforeUpload = file => {
    const isLtSize = file.size < maxSize * 1024;
    if (!isLtSize) {
      const sizeText =
        maxSize / 1024 > 1
          ? `${Number(maxSize / 1024).toFixed(2)}MB`
          : `${maxSize}KB`;
      message.error(`Image must be smaller than ${sizeText}`, 5);
    }
    return isLtSize;
  };

  // 相册删除相片之后的回调函数 -- 如果删除成功，则调用相册数据改变的回调函数
  const handleRemove = (file, fileList) => {
    const updatedFileList = fileList.filter(item => item.uid !== file.uid);
    if ((updatedFileList.length === 1 || file.isCover) && updatedFileList[0]) {
      updatedFileList[0].isCover = 1;
    }
    triggerChange(updatedFileList);
  };

  // 蒙层被点击之后的回调函数
  const handleMarkCover = (file, fileList) => {
    const updatedFileList = fileList.map(item => ({
      ...item,
      isCover: file.uid === item.uid ? 1 : 0,
    }));
    triggerChange(updatedFileList);
  };

  // 构造Upload组件所需要的itemRender函数；itemRender函数接收originNode、file和fileList三个入参，用来渲染相册
  const itemRender = (originNode, file = {}, fileList) => {
    return (
      <div className="my-album-item">
        <div className="my-album-item-content">
          <Image
            src={file.url}
            preview={false}
            height={'100%'}
            width={'100%'}
          />
          {/* 使用flex构造蒙层 */}
          {/* top: 0;
          left: 0;
          width: 100%;
          height: 100%; */}
          <Row
            className="my-album-item-action"
            justify={'center'}
            align={'middle'}
          >
            <Space>
              {file.isCover ? (
                <StarFilled className="action-icon" title="mark as cover" />
              ) : (
                <StarOutlined
                  className="action-icon"
                  title="mark as cover"
                  onClick={() => {
                    handleMarkCover(file, fileList); // 点击之后需要更新相片的选中状态
                  }}
                />
              )}
              <DeleteOutlined
                className="action-icon"
                title="delete"
                size={14}
                onClick={() => {
                  handleRemove(file, fileList); // 点击之后移除某个相片
                }}
              />
            </Space>
          </Row>
        </div>
      </div>
    );
  };

  return (
    <Row justify="start" wrap={false}>
      <Col flex="auto">
        <Upload
          accept={accept} // 接受上传文件的类型
          listType="picture-card" // 文件列表的展示方式
          fileList={value} // 文件列表
          customRequest={handleUpload} // 与后端进行网络通信
          beforeUpload={beforeUpload} // 上传前的拦截器；返回boolean表示是否允许上传
          itemRender={itemRender}
        >
          {/* 新增按钮，如果超过数量就不再展示出来 */}
          {maxCount && value?.length >= maxCount ? null : uploadButton} 
        </Upload>
      </Col>
      {/* 一些提示信息 */}
      <Col className="my-album-tips">{tips}</Col>
    </Row>
  );
}
```

## 附录
此组件的调用方式：
```js
<FormUploadAlbum
    action="/a/b/c/d/upload"
    maxCount={10}
/>
```