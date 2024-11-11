> 注：一般编码规范使用格式化工具（prettier）会自动格式化，建议将 vscode 中的“保存自动格式化”功能打开。下面列出的规范为使用格式化工具无法格式化的情况。

## 命名
### 文件/文件夹命名
> 页面（路由）文件/文件夹、css 文件、静态资源遵循一般规范（小写+中划线）命名，组件用使用帕斯卡（大驼峰）命名。

- 组件名使用帕斯卡命名。如：`ReservationCard.jsx`
- 如果整个目录是一个组件，则整个目录名使用帕斯卡命名，并且使用`index.jsx`作为入口文件。如果存在`css`文件，则将`css`文件命名为`index.css`
```js
components
├─ reservationCard.jsx // bad
├─ reservation-card.jsx // bad
├─ ReservationCard.jsx // good
└─ ReservationCard // good
    ├─ index.tsx
    └─ index.css
```
### `jsx`命名
- 组件名使用帕斯卡命名。如：`ReservationCard.jsx`
```js
// bad
import reservationCard from '@/components/ReservationCard'
<reservationCard />
// good
import ReservationCard from '@/components/ReservationCard'
<ReservationCard />
```
- 组件名与当前文件名一致. 如 ReservationCard.jsx 应该包含名为 ReservationCard 的组件
```js
// good
const ReservationCard = () => {
  return <div>this is reservation card</div>
}
export default ReservationCard
```
- 组件使用时如果没有子元素，采用自闭和的写法
```js
import ReservationCard from '@/components/ReservationCard'
// bad
<ReservationCard></ReservationCard>
// good
<ReservationCard />
// good
<ReservationCard>
  <div>this is content.</div>
</ReservationCard>
```
## 模块引入规范
- 模块如果以`index.jsx`为入口文件，则被引入时需要省略`index.jsx`
- 如果引入组件与被引入组件嵌套层级较深，使用`@/`替代`../`
```js
// bad
import ReservationCard from '@/components/ReservationCard/index.tsx'
// good
import ReservationCard from '@/components/ReservationCard'


// bad
import ReservationCard from '../../../components/ReservationCard'
// good
import ReservationCard from '@/components/ReservationCard'
```