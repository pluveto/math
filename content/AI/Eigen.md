## tensor.chip

```cpp
<Operation> chip(const Index offset, const Index dim)
```

例子：

```cpp
#include <iostream>
#include <Eigen/Dense>
#include <unsupported/Eigen/CXX11/Tensor>

int main() {
    // 创建一个2x3x4的三维张量
    Eigen::Tensor<int, 3> tensor(2, 3, 4);
    tensor.setValues({{{1, 2, 3, 4}, {5, 6, 7, 8}, {9, 10, 11, 12}},
                      {{13, 14, 15, 16}, {17, 18, 19, 20}, {21, 22, 23, 24}}});
    Eigen::Tensor<int, 2> chip = tensor.chip(0, 1);

    // 打印提取的“chip”
    std::cout << "Extracted chip:\n" << chip << std::endl;

    return 0;
}
```

```
Extracted chip:
 1  2  3  4
13 14 15 16
```

另一个例子，如果想要得到图中的矩阵，怎么做？

![image](https://raw.githubusercontent.com/pluveto/0images/master/2024/20240307115020-01664A4B-18FC-4C58-93E0-BDC12B92CF04.png)

这个平面正交于 x 轴，因此要提取的是 `index=0` 的轴，又因为正交点的 x 坐标是 0，因此参数是 `chip(0,0)`

```
Extracted chip:
 1  2  3  4
 5  6  7  8
 9 10 11 12
```