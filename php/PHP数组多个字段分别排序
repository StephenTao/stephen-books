### 1. PHP数组Array按其中一个字段排序 ### 

```
/**
* Sort array by filed and type, common utility method.
* @param array $data
* @param string $sort_filed
* @param string $sort_type SORT_ASC or SORT_DESC
*/
public function sortByOneField($data, $filed, $type)
{
    if (count($data) <= 0) {
        return $data;
    }
    foreach ($data as $key => $value) {
        $temp[$key] = $value[$filed];
    }
    array_multisort($temp, $type, $data);
    return $data;
}

```

### 2.PHP数组Array2个字段排序，先按第一个字段排序，再按第二个字段排序 ###

```
/**
 * Sort array by filed and type, common utility method.
 * @param array $array
 * @param string $filed1
 * @param string $type1 SORT_ASC or SORT_DESC
 * @param string $filed2
 * @param string $type2 SORT_ASC or SORT_DESC
 */
public function sortByTwoFiled($data, $filed1, $type1, $filed2, $type2)
{
    if (count($data) <= 0) {
        return $data;
    }
    foreach ($data as $key => $value) {
        $temp_array1[$key] = $value[$filed1];
        $temp_array2[$key] = $value[$filed2];
    }
    array_multisort($temp_array1, $type1, $temp_array2, $type2, $data);
    return $users;
}
```

### 3. 扩展方法sortMultiArray（）最多支持对数组3个字段排序，当然可以扩充的,自定义方法重载实现对多维数字的排序。 ###
使用方法:
1. sortArrayMultiFields（$data, [‘score’ => SORT_DESC]）
2. sortArrayMultiFields（$data, [‘score’ => SORT_DESC, ‘count’ => SORT_ASC]）
3. sortArrayMultiFields（$data, [‘score’ => SORT_DESC, ‘count’ => SORT_ASC, ‘name’ => SORT_ASC]）

```
/**
 * Sort multi array by filed and type.
 * @param data $array
 * @param condition $array
 */
public function sortArrayMultiFields(&$data, $condition)
{
    if (count($data) <= 0 || empty($condition)) {
        return $data;
    }
    $fieldsCount = count($condition);
    $fileds = array_keys($condition);
    $types = array_values($condition);
    switch ($fieldsCount) {
        case 1:
            $data = $this->sort1Field($data, $fileds[0], $types[0]);
            break;
        case 2:
            $data = $this->sort2Fields($data, $fileds[0], $types[0], $fileds[1], $types[1]);
            break;
        default:
            $data = $this->sort3Fields($data, $fileds[0], $types[0], $fileds[1], $types[1], $fileds[2], $types[2]);
            break;
    }
    return $data;
}

public function sort1Field(&$data, $filed, $type)
{
    if (count($data) <= 0) {
        return $data;
    }
    foreach ($data as $key => $value) {
        $temp[$key] = $value[$filed];
    }
    array_multisort($temp, $type, $data);
    return $data;
}

public function sort2Fields(&$data, $filed1, $type1, $filed2, $type2)
{
    if (count($data) <= 0) {
        return $data;
    }
    foreach ($data as $key => $value) {
        $sort_filed1[$key] = $value[$filed1];
        $sort_filed2[$key] = $value[$filed2];
    }
    array_multisort($sort_filed1, $type1, $sort_filed2, $type2, $data);
    return $data;
}

public function sort3Fields(&$data, $filed1, $type1, $filed2, $type2, $filed3, $type3)
{
    if (count($data) <= 0) {
        return $data;
    }
    foreach ($data as $key => $value) {
        $sort_filed1[$key] = $value[$filed1];
        $sort_filed2[$key] = $value[$filed2];
        $sort_filed3[$key] = $value[$filed3];
    }
    array_multisort($sort_filed1, $type1, $sort_filed2, $type2, $sort_filed3, $type3, $data);
    return $data;
}
```
