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

## `print`関数のフォーマット

Rustにおける`print`関数は`std::fmt`で定義された一連のマクロで処理される。

* `format!`：フォーマットされたテキストを`String`に変換する
* `print!`：`format!`と同じだが、コンソール(`io::stdout`)にテキストを出力する
* `printin!`：`print!`と同じだが、改行が追加される
* `eprint!`：`format!`と同じだが、テキストを標準エラー出力(`io::stderr`)にする
* `eprintin!`：`eprint`と仕組みは同じだが、改行が追加される

どれも同じようにテキストをパースする。なお、Rustはコンパイル時に書式の正しさをチェックする。

```rs
fn main() {
    // In general, the `{}` will be automatically replaced with any
    // arguments. These will be stringified.
    println!("{} days", 31);

    // Without a suffix, 31 becomes an i32. You can change what type 31 is
    // by providing a suffix. The number 31i64 for example has the type i64.

    // There are various optional patterns this works with. Positional
    // arguments can be used.
    println!("{0}, this is {1}. {1}, this is {0}", "Alice", "Bob");

    // As can named arguments.
    println!("{subject} {verb} {object}",
             object="the lazy dog",
             subject="the quick brown fox",
             verb="jumps over");

    // Special formatting can be specified after a `:`.
    println!("{} of {:b} people know binary, the other half doesn't", 1, 2);

    // You can right-align text with a specified width. This will output
    // "     1". 5 white spaces and a "1".
    println!("{number:>width$}", number=1, width=6);

    // You can pad numbers with extra zeroes. This will output "000001".
    println!("{number:0>width$}", number=1, width=6);

    let number: f64 = 1.0;
    let width: usize = 6;
    println!("{number:>width$}");
}

// 実行結果
// --------- 
// 31 days
// Alice, this is Bob. Bob, this is Alice
// the quick brown fox jumps over the lazy dog
// 1 of 10 people know binary, the other half doesn't
//      1
// 000001
//      1
```

## リテラル

整数`1`、浮動小数点`1.2`、文字`'a'`、文字列`"abc"、真偽値`true`や単位型はリテラルを用いて表現できる。

整数は16進数、8進数、2進数にそれぞれ接頭辞を付けて表現できる。(例：`0x`, `0o`と`0b`)読みやすくするために、数値リテラルにアンダースコア(`_`)を挿入できる。

コンパイラに使用するリテラルの型を伝える必要がある。今のところ、リテラルが符号なし32ビット整数であることを示すには`i32`サフィックスを使うことにする。

Rustで使用できる演算子とその優先順位は、他のC系言語と同じ。


```rs
fn main() {
    // Integer addition
    println!("1 + 2 = {}", 1u32 + 2);

    // Integer subtraction
    println!("1 - 2 = {}", 1i32 - 2);
    // TODO ^ Try changing `1i32` to `1u32` to see why the type is important

    // Short-circuiting boolean logic
    println!("true AND false is {}", true && false);
    println!("true OR false is {}", true || false);
    println!("NOT true is {}", !true);

    // Bitwise operations
    println!("0011 AND 0101 is {:04b}", 0b0011u32 & 0b0101);
    println!("0011 OR 0101 is {:04b}", 0b0011u32 | 0b0101);
    println!("0011 XOR 0101 is {:04b}", 0b0011u32 ^ 0b0101);
    println!("1 << 5 is {}", 1u32 << 5);
    println!("0x80 >> 2 is 0x{:x}", 0x80u32 >> 2);

    // Use underscores to improve readability!
    println!("One million is written as {}", 1_000_000u32);
}
```

## タプル(`tuple`)

タプルとは異なる型の集まりである。タプルは`()`で構成され、各タプル自体は型シグネチャ`(T1, T2)`を持つ値であり、`T1`と`T2`はそのメンバーの型である。

タプルは任意の数の値を保持できるので、関数はタプルを使って複数の値を返せる。

```rs
// Tuples can be used as function arguments and as return values
fn reverse(pair: (i32, bool)) -> (bool, i32) {
    // `let` can be used to bind the members of a tuple to variables
    let (integer, boolean) = pair;

    (boolean, integer)
}

// The following struct is for the activity.
#[derive(Debug)]
struct Matrix(f32, f32, f32, f32);

fn main() {
    // A tuple with a bunch of different types
    let long_tuple = (1u8, 2u16, 3u32, 4u64,
                      -1i8, -2i16, -3i32, -4i64,
                      0.1f32, 0.2f64,
                      'a', true);

    // Values can be extracted from the tuple using tuple indexing
    println!("long tuple first value: {}", long_tuple.0);
    println!("long tuple second value: {}", long_tuple.1);

    // Tuples can be tuple members
    let tuple_of_tuples = ((1u8, 2u16, 2u32), (4u64, -1i8), -2i16);

    // Tuples are printable
    println!("tuple of tuples: {:?}", tuple_of_tuples);
    
    // But long Tuples cannot be printed
    // let too_long_tuple = (1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13);
    // println!("too long tuple: {:?}", too_long_tuple);
    // TODO ^ Uncomment the above 2 lines to see the compiler error

    let pair = (1, true);
    println!("pair is {:?}", pair);

    println!("the reversed pair is {:?}", reverse(pair));

    // To create one element tuples, the comma is required to tell them apart
    // from a literal surrounded by parentheses
    println!("one element tuple: {:?}", (5u32,));
    println!("just an integer: {:?}", (5u32));

    //tuples can be destructured to create bindings
    let tuple = (1, "hello", 4.5, true);

    let (a, b, c, d) = tuple;
    println!("{:?}, {:?}, {:?}, {:?}", a, b, c, d);

    let matrix = Matrix(1.1, 1.2, 2.1, 2.2);
    println!("{:?}", matrix);

}
```

## 配列とスライス

配列は、同じ型`T`のオブジェクトの集まりで、連続したメモリに格納されている。配列は`[]`で作られ、その長さはコンパイル時に既知で型シグネチャの一部`[T: length]`になる。

スライスは配列と似ているが、コンパイル時には長さがわからない。その代わり、スライスは2単語以上のオブジェクトで、最初の単語はデータへのポインターで、2番目の単語はスライスの長さである。

ワードのサイズは`usize`と同じで、プロセッサのアーキテクチャによって決まる。スライスは配列の一部を借用するために使われ、`&[T]`という型シグネチャを持っている。

```rs
use std::mem;

// This function borrows a slice
fn analyze_slice(slice: &[i32]) {
    println!("first element of the slice: {}", slice[0]);
    println!("the slice has {} elements", slice.len());
}

fn main() {
    // Fixed-size array (type signature is superfluous)
    let xs: [i32; 5] = [1, 2, 3, 4, 5];

    // All elements can be initialized to the same value
    let ys: [i32; 500] = [0; 500];

    // Indexing starts at 0
    println!("first element of the array: {}", xs[0]);
    println!("second element of the array: {}", xs[1]);

    // `len` returns the count of elements in the array
    println!("number of elements in array: {}", xs.len());

    // Arrays are stack allocated
    println!("array occupies {} bytes", mem::size_of_val(&xs));

    // Arrays can be automatically borrowed as slices
    println!("borrow the whole array as a slice");
    analyze_slice(&xs);

    // Slices can point to a section of an array
    // They are of the form [starting_index..ending_index]
    // starting_index is the first position in the slice
    // ending_index is one more than the last position in the slice
    println!("borrow a section of the array as a slice");
    analyze_slice(&ys[1 .. 4]);

    // Out of bound indexing causes compile error
    println!("{}", xs[5]);
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