按照业务需求，需要封装一个组件。这个组件的功能为，显示为两个DatePicker，第一个是起始时间，第二个是终止时间；起始时间和终止时间可以不填，他们都有默认值；但是如果两个时间都存在的时候就必须保证结束时间不小于开始时间。

在这个组件封装的过程中，比较重要的几点在于：
- 1. 使用antd组件并对其封装
- 2. 使用styled-components强行改变antd组件内部样式
- 3. 使用moment库对选择的日期进行序列化和反序列化
- 4. 使用国际化字段及相关的方法getIntl
- 5. 使用antd提供的Row Col来排布页面中的元素
- 6. 使用Form.Item中的rules属性来保证结束日期大于开始日期
- 7. 使用IDatePicker组件的disableData来自定义选择日期为今天以前

封装组件内容：
```jsx
import React from 'react';
import { Row, Col, Form, DatePicker } from 'antd';
import styled from 'styled-components';
import { getIntl } from '../../utils';

const ILabel = styled.label`
  &::after {
    content: ':';
    position: relative;
    top: -0.5px;
    margin: 0 8px 0 2px;
  }
`;

const IDatePicker = styled(DatePicker)`
  &.ant-picker {
    width: 100% !important;
  }
`;

const CustomDatePicker = ({
  formRef,
  disabledDate,
  span = 5,
  itemName = 'displayPeriod',
  startDateName = 'listingDate',
  endDateName = 'deListingDate',
  dateSpan = 10,
  arrowSpan = 3,
  arrowPosition = 'start',
}) => {
  return (
    <Row>
      <Col span={span} style={{ padding: 0, textAlign: 'end' }}>
        <ILabel for={itemName} title={itemName}>{`${getIntl(
          'NF.Content',
        )}`}</ILabel>
      </Col>
      <Col span={24 - span} style={{ padding: 0 }}>
        <Row className="ant-form-item">
          <Col span={dateSpan}>
            <Row>
              <Form.Item
                className="custom-date-picker"
                name={startDateName}
                style={{ marginBottom: 6, width: '100%' }}
                help="It will be visible after the start date. Visible immediately when empty."
              >
                <IDatePicker
                  placeholder="Select start date"
                  // value={timeRange}
                  format="MM/DD, YYYY"
                  // onChange={onlistingDateChange}
                  disabledDate={disabledDate}
                />
              </Form.Item>
            </Row>
          </Col>
          <Col span={arrowSpan}>
            <Row
              style={{
                height: 31.6,
                display: 'flex',
                alignItems: 'center',
                justifyContent: arrowPosition,
              }}
            >
              <svg
                width="12"
                height="6"
                viewBox="0 0 12 6"
                fill="none"
                xmlns="http://www.w3.org/2000/svg"
              >
                <path
                  d="M11.6402 4.31563L9.07767 1.06563C9.03094 1.00629 8.97138 0.958321 8.90346 0.925306C8.83553 0.892291 8.76101 0.875092 8.68549 0.875H7.67299C7.5683 0.875 7.51049 0.995312 7.57455 1.07812L9.82924 3.9375H0.372986C0.304236 3.9375 0.247986 3.99375 0.247986 4.0625V5C0.247986 5.06875 0.304236 5.125 0.372986 5.125H11.2464C11.6652 5.125 11.898 4.64375 11.6402 4.31563Z"
                  fill="black"
                  fill-opacity="0.25"
                />
              </svg>
            </Row>
          </Col>
          <Col span={dateSpan}>
            <Row>
              <Form.Item
                className="custom-date-picker"
                name={endDateName}
                style={{ marginBottom: 6, width: '100%' }}
                // onChange={onDelistingDateChange}
                help="It will be invisible after the end date. Always visible when empty."
                rules={[
                  {
                    validator: () => {
                      const values = formRef.current?.getFieldsValue();
                      const listingDate = values[startDateName];
                      const deListingDate = values[endDateName];
                      if (deListingDate && listingDate) {
                        const compare = deListingDate.diff(listingDate);

                        if (compare > 0) {
                          return Promise.resolve();
                        }
                        return Promise.reject('Please upload');
                      }
                      return Promise.resolve();
                    },
                    message: `The end date must be later than the start date`,
                  },
                ]}
              >
                <IDatePicker
                  placeholder="Select end date"
                  // value={timeRange}
                  format="MM/DD, YYYY"
                  // onChange={onDelistingDateChange}
                  disabledDate={disabledDate}
                />
              </Form.Item>
            </Row>
          </Col>
        </Row>
      </Col>
    </Row>
  );
};

export default CustomDatePicker;

```

自定义日期规则：
```jsx
// 自定义disabledDate函数
function disabledDate(current) {
  const today = moment();
  const nextYear = moment().add(365, 'days');
  return current && (current < today || current > nextYear);
}

```