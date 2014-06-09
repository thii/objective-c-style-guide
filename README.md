# Objective-Cスタイルガイド

[ニューヨーク・タイムズ Objective-Cスタイルガイド]((https://github.com/NYTimes/objetive-c-style-guide)からフォークした日本語版になります。Issueが見つかったら[こちら](https://github.com/thii/objective-c-style-guide/issues)にあげてください。


## はじめに

こちらのスタイルガイドに述べないことはアップルのガイドラインを参考してください(PDFファイル)。

* [Coding Guidelines for Cocoa](https://developer.apple.com/jp/devcenter/ios/library/documentation/CodingGuidelines.pdf)

## 目次

* [ドットノーテーションシンタックス](#dot-notation-syntax)
* [スペーシング](#spacing)
* [条件](#conditionals)
  * [三項演算子](#ternary-operator)
* [エラー処理](#error-handling)
* [メソッド](#methods)
* [変数](#variables)
* [命名](#naming)
* [コメント](#comments)
* [初期化](#init)
* [リテラル](#literals)
* [CGRect 関数](#cgrect-functions)
* [定数](#constants)
* [列挙型定数](#enumerated-types)
* [ビットマスク](#bitmasks)
* [プライベートプロパティ](#private-properties)
* [画像命名](#image-naming)
* [ブーリアン](#booleans)
* [シングルトン](#singletons)
* [Xcodeプロジェクト](#xcode-project)

## ドットノーテーションシンタックス

プロパティのアクセスや変更する際に**必ず**ドットノーテーションを使うこと。それ以外の場合は大括弧を使うこと。 

**良い例：**
```objc
view.backgroundColor = [UIColor orangeColor];
[UIApplication sharedApplication].delegate;
```

**悪い例：**
```objc
[view setBackgroundColor:[UIColor orangeColor]];
UIApplication.sharedApplication.delegate;
```

## スペーシング

* スペース4つインデントすること。タブキーでインデントしない。Xcodeで設定するように。
* 条件分岐など(`if`/`else`/`switch`/`while` etc.)のメソッドブレースを同じ行で開き、改行で閉じること。

**良い例：**
```objc
if (user.isHappy) {
    // なんとかする
} else {
    // 他のをする
}
```
* メソッドとメソッドは1行を開くこと。メソッド内で処理ごとに1行を開く。
* `@dynamic`を一行ずつ書くこと。

## 条件分岐

条件分岐の処理を中括弧の中に書くこと。

**良い例：**
```objc
if (!error) {
    return success;
}
```

**悪い例：**
```objc
if (!error)
    return success;
```

or

```objc
if (!error) return success;
```

### 三項演算子

三項演算子をわかりやすくなる場合のみ使うこと。

**良い例：**
```objc
result = a > b ? x : y;
```

**悪い例：**
```objc
result = a > b ? x = c > d ? c : d : y;
```

## エラー処理

メソッドがエラーの返戻値の参照を返す場合、条件はエラー変数を使わずに、返戻値を使うこと。

**良い例：**
```objc
NSError *error;
if (![self trySomethingWithError:&error]) {
    // Handle Error
}
```

**悪い例：**
```objc
NSError *error;
[self trySomethingWithError:&error];
if (error) {
    // Handle Error
}
```

いくつかのAppleのAPIでは、サクセスする場合エラー変数にゴミを入れられるので条件がNOになってしまう場合がある。

## メソッド

メソッドのスコープの後にスペースを開けること。

**良い例：**
```objc
- (void)setExampleText:(NSString *)text image:(UIImage *)image;
```
## 変数

変数名をわかりやすいように命名してください。`for()` 文以外は一文字の変数名をしないこと。

米印は変数のもの。定数は例外。

**良い例：**

`NSString *text`

**悪い例：**

`NSString* text`
`NSString * text`

できればインスタンス変数を使わずにプロパティで宣言すること。また、初期化メソッド(`init`, `initWithCoder:`, etc…)以外はインスタンス変数を使わないこと。

**良い例：**

```objc
@interface DTTSection: NSObject

@property (nonatomic) NSString *headline;

@end
```

**悪い例：**

```objc
@interface DTTSection : NSObject {
    NSString *headline;
}
```

## 命名

長くて、わかりやすいメソッド名と変数名が良い。

**良い例：**

```objc
UIButton *settingsButton;
```

**良い例：**

```objc
UIButton *setBut;
```

定数名をキャメルケースで最初の文字を大文字。

**良い例：**

```objc
static const NSTimeInterval ArticleViewControllerNavigationFadeAnimationDuration = 0.3;
```

**悪い例：**

```objc
static const NSTimeInterval fadetime = 1.7;
```

プロパティ名とローカル変数名をキャメルケースで最初の文字を小文字。

LLVMは自動的にsynthesisしてくれるのでsynthesisをしないこと。

## コメント

コメントは必要の場合のみする。何故か書いたコードを何の処理ができるか書くように。コメントの更新することを忘れずに。必要がなくなったら削除すること。

ブロックコメントをなるべくしないこと。コードがセルフドキュメンティングしてもらうように。ドキュメンテーション生成用のコメントは例外。

## 初期化

`init` メソッドを以下のように書くこと。

```objc
- (instancetype)init {
    self = [super init]; // または指定の初期化メソッド
    if (self) {
        // カスタマイズ初期化
    }

    return self;
}
```

## リテラル

`NSString`、`NSDictionary`、`NSArray`、と `NSNumber`リテラルを使うように。`nil` を`NSArray`や`NSDictionary` リテラルに入れてしますとクラッシュするので入れないように。

**良い例：**

```objc
NSArray *names = @[@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul"];
NSDictionary *productManagers = @{@"iPhone" : @"Kate", @"iPad" : @"Kamal", @"Mobile Web" : @"Bill"};
NSNumber *shouldUseLiterals = @YES;
NSNumber *buildingZIPCode = @10018;
```

**悪い例：**

```objc
NSArray *names = [NSArray arrayWithObjects:@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul", nil];
NSDictionary *productManagers = [NSDictionary dictionaryWithObjectsAndKeys: @"Kate", @"iPhone", @"Kamal", @"iPad", @"Bill", @"Mobile Web", nil];
NSNumber *shouldUseLiterals = [NSNumber numberWithBool:YES];
NSNumber *buildingZIPCode = [NSNumber numberWithInteger:10018];
```

## CGRect 関数

`CGRect`の`x`, `y`, `width`, と `height`をアクセスする際、構造体メンバーをアクセスせずに**必ず** [`CGGeometry` 関数](http://developer.apple.com/library/ios/#documentation/graphicsimaging/reference/CGGeometry/Reference/reference.html) を使うこと。Appleの `CGGeometry` レファレンス:

> All functions described in this reference that take CGRect data structures as inputs implicitly standardize those rectangles before calculating their results. For this reason, your applications should avoid directly reading and writing the data stored in the CGRect data structure. Instead, use the functions described here to manipulate rectangles and to retrieve their characteristics.

**良い例：**

```objc
CGRect frame = self.view.frame;

CGFloat x = CGRectGetMinX(frame);
CGFloat y = CGRectGetMinY(frame);
CGFloat width = CGRectGetWidth(frame);
CGFloat height = CGRectGetHeight(frame);
```

**悪い例：**

```objc
CGRect frame = self.view.frame;

CGFloat x = frame.origin.x;
CGFloat y = frame.origin.y;
CGFloat width = frame.size.width;
CGFloat height = frame.size.height;
```

## 定数

数値やin-line文字列をなるべく定数で宣言するように。あとでリプロダクション時変更しやすいので。定数を`#define`ではなく`static`で宣言するように。

**良い例：**

```objc
static NSString * const AboutViewControllerCompanyName = @"Apple Inc.";

static const CGFloat ImageThumbnailHeight = 50.0;
```

**悪い例：**

```objc
#define CompanyName @"Apple Inc."

#define thumbnailHeight 2
```

## 列挙型定数

列挙型定数を宣言する際、`enum`ではなく新しい列挙型`NS_ENUM()`を使うように。

**良い例：**
```objc
typedef NS_ENUM(NSInteger, AdRequestState) {
    AdRequestStateInactive,
    AdRequestStateLoading
};
```

## ビットマスク

ビットマスクを使う時に`NS_OPTIONS`で。

**良い例：**
```objc
typedef NS_OPTIONS(NSUInteger, AdCategory) {
    AdCategoryAutos      = 1 << 0,
    AdCategoryJobs       = 1 << 1,
    AdCategoryRealState  = 1 << 2,
    AdCategoryTechnology = 1 << 3
};
```

## プライベートプロパティ

プライベートプロパティをインプレメンテーションファイルのクラス拡張に書き、カテゴリ名を空に。

**良い例：**

```objc
@interface DTTAdvertisement ()

@property (nonatomic, strong) GADBannerView *googleAdView;
@property (nonatomic, strong) ADBannerView *iAdView;
@property (nonatomic, strong) UIWebView *adXWebView;

@end
```

## 画像ファイル名

動作名+クラス名あるいはプロパティ名+状態の形で。

**良い例：**

* `RefreshBarButtonItem` / `RefreshBarButtonItem@2x` and `RefreshBarButtonItemSelected` / `RefreshBarButtonItemSelected@2x`
* `ArticleNavigationBarWhite` / `ArticleNavigationBarWhite@2x` and `ArticleNavigationBarBlackSelected` / `ArticleNavigationBarBlackSelected@2x`.

Imagesフォルダに同じ目的の画像をグループで分ける。

## ブーリアン

`nil`が `NO`に分解するので`nil`と比較するの不要。`YES`と比較しないこと。`YES`が1だが`BOOL`8ビットで表すので。

**良い例：**

```objc
if (!someObject) {
}
```

**悪い例：**

```objc
if (someObject == nil) {
}
```

-----

**良い例：**

```objc
if (isAwesome)
if (![someObject boolValue])
```

**悪い例：**

```objc
if ([someObject boolValue] == NO)
if (isAwesome == YES) // Never do this.
```

-----

`BOOL`プロパティ名が形容詞なら、“is”プレフィックスが不要だがゲッター名を指定するように。

```objc
@property (assign, getter=isEditable) BOOL editable;
```

## シングルトン

[クラッシュの可能性](http://cocoasamurai.blogspot.com/2011/04/singletons-your-doing-them-wrong.html)を避けるため、スレッドセーフパターンでシングルトンを作成するように。

```objc
+ (instancetype)sharedInstance {
   static id sharedInstance = nil;

   static dispatch_once_t onceToken;
   dispatch_once(&onceToken, ^{
      sharedInstance = [[self alloc] init];
   });

   return sharedInstance;
}
```

## Xcode プロジェクト

わかりやすいようにXcodeのグループとプロジェクトのフォルダが同じように。型でグループするだけではなく、機能ごとにグループするように。

できれば、targetの Build Settingsの"Treat Warnings as Errors"をオンにし、多めに[アディショナルワーニング](http://boredzo.org/blog/archives/2009-11-07/warnings) をオンにする。特定のワーニングをオフしたい場合、[Clang's pragma 機能](http://clang.llvm.org/docs/UsersManual.html#controlling-diagnostics-via-pragmas)を使う。

# 他のObjective-Cスタイルガイド

* [Google](http://google-styleguide.googlecode.com/svn/trunk/objcguide.xml)
* [GitHub](https://github.com/github/objective-c-conventions)
* [Adium](https://trac.adium.im/wiki/CodingStyle)
* [Sam Soffes](https://gist.github.com/soffes/812796)
* [CocoaDevCentral](http://cocoadevcentral.com/articles/000082.php)
* [Luke Redpath](http://lukeredpath.co.uk/blog/my-objective-c-style-guide.html)
* [Marcus Zarra](http://www.cimgf.com/zds-code-style-guide/)
