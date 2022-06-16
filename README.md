# Rust Sample

Rust言語を試しに動かしてみたサンプルプロダクト。

以下は、Rustで`Hello World`を出力するプログラム。

```rs
fn main() {
    println!("Hello World");
}
```

# 基本内容

Rustでは、基本的に`main`関数の中に実行する処理を書く。(**DartやKotlinと類似している**)

## データ型

Rustで扱うデータ型は以下の通り。PythonやTypeScriptよりも細かく分類されており、非常に多い。

* 符号付き整数(`i8`, `i16`, `i32`, `i64`, `i128`, `isize`)
* 符号なし整数(`u8`, `u16`, `u32`, `u64`, `u128`, `usize`)
* 浮動小数点(`f32`, `f64`)
* `char`型：一文字の文字列
* 真偽値(`bool`)：`true`あるいは`false`
* 単位型：空のtuple
* 配列：`[1, 2, 3]`
* タプル(`tuple`)：`(1, false)`

変数は常に型のアノテーションを付けられる。数値はサフィックスあるいはデフォルトでアノテーションを付けられる。整数のデフォルトは`i32`で、浮動小数点のデフォルトは`f64`である。

あと、Rustは文脈から型を推定できる。

```rs
fn main() {
    // Variables can be type annotated.
    let logical: bool = true;

    let a_float: f64 = 1.0;  // Regular annotation
    let an_integer   = 5i32; // Suffix annotation

    // Or a default will be used.
    let default_float   = 3.0; // `f64`
    let default_integer = 7;   // `i32`
    
    // A type can also be inferred from context 
    let mut inferred_type = 12; // Type i64 is inferred from another line
    inferred_type = 4294967296i64;
    
    // A mutable variable's value can be changed.
    let mut mutable = 12; // Mutable `i32`
    mutable = 21;
    
    // Error! The type of a variable can't be changed.
    mutable = true;
    
    // Variables can be overwritten with shadowing.
    let mutable = true;
}
```

# サンプルプログラム

## 素数判定

```rs
pub fn prime_numbers(max: usize) -> Vec<usize> {
    let mut result: Vec<usize> = Vec::new();

    if max >= 2 {
        result.push(2)
    }
    for i in (3..max + 1).step_by(2) {
        let stop: usize = (i as f64).sqrt() as usize + 1;
        let mut status: bool = true;

        for j in (3..stop).step_by(2) {
            if i % j == 0 {
                status = false
            }
        }
        if status {
            result.push(i)
        }
    }

    result
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn basic() {
        assert_eq!(prime_numbers(0), vec![]);
        assert_eq!(prime_numbers(11), vec![2, 3, 5, 7, 11]);
        assert_eq!(prime_numbers(25), vec![2, 3, 5, 7, 11, 13, 17, 19, 23]);
        assert_eq!(
            prime_numbers(33),
            vec![2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31]
        );
    }
}
```

## カウントソート

カウントソートは、特定の範囲内のキーに基づくソート技術。これは、異なるキーの値を持つオブジェクトの数を数えることで動作する。(いわゆるハッシュ化の一種)

そして、出力シーケンスにおける各オブジェクトの位置を計算するために、いくつかの算術演算を実施する。

```rs
pub fn counting_sort(arr: &mut [u32], maxval: usize) {
    let mut occurences: Vec<usize> = vec![0; maxval + 1];

    for &data in arr.iter() {
        occurences[data as usize] += 1;
    }

    let mut i = 0;
    for (data, &number) in occurences.iter().enumerate() {
        for _ in 0..number {
            arr[i] = data as u32;
            i += 1;
        }
    }
}

use std::ops::AddAssign;

pub fn generic_counting_sort<T: Into<u64> + From<u8> + AddAssign + Copy>(
    arr: &mut [T],
    maxval: usize,
) {
    let mut occurences: Vec<usize> = vec![0; maxval + 1];

    for &data in arr.iter() {
        occurences[data.into() as usize] += 1;
    }

    // Current index in output array
    let mut i = 0;

    // current data point, necessary to be type-safe
    let mut data = T::from(0);

    // This will iterate from 0 to the largest data point in `arr`
    // `number` contains the occurances of the data point `data`
    for &number in occurences.iter() {
        for _ in 0..number {
            arr[i] = data;
            i += 1;
        }

        data += T::from(1);
    }
}

#[cfg(test)]
mod test {
    use super::super::is_sorted;
    use super::*;

    #[test]
    fn counting_sort_descending() {
        let mut ve1 = vec![6, 5, 4, 3, 2, 1];
        counting_sort(&mut ve1, 6);

        assert!(is_sorted(&ve1));
    }

    #[test]
    fn counting_sort_pre_sorted() {
        let mut ve2 = vec![1, 2, 3, 4, 5, 6];
        counting_sort(&mut ve2, 6);

        assert!(is_sorted(&ve2));
    }

    #[test]
    fn generic_counting_sort() {
        let mut ve1: Vec<u8> = vec![100, 30, 60, 10, 20, 120, 1];
        super::generic_counting_sort(&mut ve1, 120);

        assert!(is_sorted(&ve1));
    }

    #[test]
    fn presorted_u64_counting_sort() {
        let mut ve2: Vec<u64> = vec![1, 2, 3, 4, 5, 6];
        super::generic_counting_sort(&mut ve2, 6);

        assert!(is_sorted(&ve2));
    }
}
// 実行するとエラーが生じる。現時点では原因を特定できていない。
```

# 感想

初めてRustを触ってみたけど意味不明すぎた。

# 参考

[Rust Playground](https://play.rust-lang.org/)