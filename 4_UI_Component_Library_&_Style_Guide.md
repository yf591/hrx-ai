
# 4. UI Component Library & Style Guide

これは、HRX-AIアプリケーションを開発する際の一貫したデザイン言語とコンポーネント実装のための包括的な参考資料です。このドキュメントに基づき、一貫性のある高品質なユーザーインターフェースを構築することが望ましいです。新しいコンポーネントの追加や既存コンポーネントの変更が必要な場合は、このガイドラインに沿って行い、必要に応じてドキュメントを更新するようにしてください。

## 目次

1. [デザイン原則](#1-デザイン原則)
2. [ブランドガイドライン](#2-ブランドガイドライン)
   - [カラーシステム](#21-カラーシステム)
   - [タイポグラフィ](#22-タイポグラフィ)
   - [アイコノグラフィ](#23-アイコノグラフィ)
   - [スペーシングシステム](#24-スペーシングシステム)
3. [コンポーネントライブラリ](#3-コンポーネントライブラリ)
   - [基本コンポーネント](#31-基本コンポーネント)
   - [フォーム要素](#32-フォーム要素)
   - [ナビゲーション](#33-ナビゲーション)
   - [データ表示](#34-データ表示)
   - [フィードバック](#35-フィードバック)
   - [オーバーレイ](#36-オーバーレイ)
   - [AIインターフェース](#37-aiインターフェース)
4. [レイアウトシステム](#4-レイアウトシステム)
   - [グリッドシステム](#41-グリッドシステム)
   - [ページテンプレート](#42-ページテンプレート)
   - [レスポンシブデザイン](#43-レスポンシブデザイン)
5. [状態と変異](#5-状態と変異)
6. [アニメーションとトランジション](#6-アニメーションとトランジション)
7. [アクセシビリティガイドライン](#7-アクセシビリティガイドライン)
8. [実装ガイド](#8-実装ガイド)
9. [使用例とパターン](#9-使用例とパターン)
10. [カスタマイズと拡張](#10-カスタマイズと拡張)

## 1. デザイン原則

HRX-AIのデザインは以下の原則に基づいています

### 明快性（Clarity）
情報とアクションが明確に理解できるインターフェースを提供します。ユーザーが混乱することなく重要な人事データを理解し、意思決定を行えるようにします。

### データ優先（Data-First）
複雑なデータを理解しやすい形で表示します。視覚的優先順位を設定し、最も重要な情報が最も目立つようにします。

### 効率性（Efficiency）
最小限のステップでタスクを完了できるようにします。人事業務の効率化というHRX-AIのコア価値を反映したデザインです。

### 信頼性（Trustworthiness）
プロフェッショナルで信頼できる印象を与えるデザインを採用し、機密性の高い人事データを扱うアプリケーションとして安心感を提供します。

### 応答性（Responsiveness）
ユーザーアクションに対して即座にフィードバックを提供し、処理中の状態を明確に示します。特にAI処理を待つ際のユーザー体験を最適化します。

### 進化性（Evolutive）
ユーザーの習熟度に応じてインターフェースが適応し、初心者には簡潔なガイダンスを、上級者には高度な機能へのアクセスを提供します。

## 2. ブランドガイドライン

### 2.1 カラーシステム

#### プライマリーカラー

プライマリーカラーは、ブランドの主要な識別子として使用されます。

```typescript
// tailwind.config.js
const colors = {
  primary: {
    50: '#eef2ff',
    100: '#e0e7ff', 
    200: '#c7d2fe',
    300: '#a5b4fc',
    400: '#818cf8',
    500: '#6366f1', // メインのプライマリー色
    600: '#4f46e5',
    700: '#4338ca',
    800: '#3730a3',
    900: '#312e81',
    950: '#1e1b4b',
  },
  // ...
}
```

#### セカンダリーカラー

セカンダリーカラーは、視覚的階層を作り出し、UIの特定の要素を強調するために使用されます。

```typescript
// tailwind.config.js
const colors = {
  // ...
  secondary: {
    50: '#f0fdfa',
    100: '#ccfbf1',
    200: '#99f6e4',
    300: '#5eead4',
    400: '#2dd4bf',
    500: '#14b8a6', // メインのセカンダリー色
    600: '#0d9488',
    700: '#0f766e',
    800: '#115e59',
    900: '#134e4a',
    950: '#042f2e',
  },
  // ...
}
```

#### アクセントカラー

アクセントカラーは、重要なアクションボタンや注目すべき情報に使用されます。

```typescript
// tailwind.config.js
const colors = {
  // ...
  accent: {
    50: '#eff6ff',
    100: '#dbeafe',
    200: '#bfdbfe',
    300: '#93c5fd',
    400: '#60a5fa',
    500: '#3b82f6', // メインのアクセント色
    600: '#2563eb',
    700: '#1d4ed8',
    800: '#1e40af',
    900: '#1e3a8a',
    950: '#172554',
  },
  // ...
}
```

#### ニュートラルカラー

ニュートラルカラーは、テキストやバックグラウンドなどの基本要素に使用されます。

```typescript
// tailwind.config.js
const colors = {
  // ...
  neutral: {
    50: '#fafafa',
    100: '#f5f5f5',
    200: '#e5e5e5',
    300: '#d4d4d4',
    400: '#a3a3a3',
    500: '#737373', // メインのテキストカラー
    600: '#525252',
    700: '#404040',
    800: '#262626',
    900: '#171717',
    950: '#0a0a0a',
  },
  // ...
}
```

#### セマンティックカラー

情報の種類を示すために使用されるセマンティックカラーです。

```typescript
// tailwind.config.js
const colors = {
  // ...
  success: {
    50: '#f0fdf4',
    100: '#dcfce7',
    200: '#bbf7d0',
    300: '#86efac',
    400: '#4ade80',
    500: '#22c55e', // メインの成功色
    600: '#16a34a',
    700: '#15803d',
    800: '#166534',
    900: '#14532d',
    950: '#052e16',
  },
  warning: {
    50: '#fffbeb',
    100: '#fef3c7',
    200: '#fde68a',
    300: '#fcd34d',
    400: '#fbbf24',
    500: '#f59e0b', // メインの警告色
    600: '#d97706',
    700: '#b45309',
    800: '#92400e',
    900: '#78350f',
    950: '#451a03',
  },
  danger: {
    50: '#fef2f2',
    100: '#fee2e2',
    200: '#fecaca',
    300: '#fca5a5',
    400: '#f87171',
    500: '#ef4444', // メインの危険色
    600: '#dc2626',
    700: '#b91c1c',
    800: '#991b1b',
    900: '#7f1d1d',
    950: '#450a0a',
  },
  info: {
    50: '#f0f9ff',
    100: '#e0f2fe',
    200: '#bae6fd',
    300: '#7dd3fc',
    400: '#38bdf8',
    500: '#0ea5e9', // メインの情報色
    600: '#0284c7',
    700: '#0369a1',
    800: '#075985',
    900: '#0c4a6e',
    950: '#082f49',
  },
  // ...
}
```

### 2.2 タイポグラフィ

HRX-AIは以下のフォントスタックを使用します

```typescript
// tailwind.config.js
const fontFamily = {
  sans: [
    'Inter var',
    'ui-sans-serif',
    'system-ui',
    '-apple-system',
    'BlinkMacSystemFont',
    '"Segoe UI"',
    'Roboto',
    '"Helvetica Neue"',
    'Arial',
    '"Noto Sans"',
    'sans-serif',
    '"Apple Color Emoji"',
    '"Segoe UI Emoji"',
    '"Segoe UI Symbol"',
    '"Noto Color Emoji"',
  ],
  mono: [
    'JetBrains Mono',
    'ui-monospace',
    'SFMono-Regular',
    'Menlo',
    'Monaco',
    'Consolas',
    '"Liberation Mono"',
    '"Courier New"',
    'monospace',
  ],
}
```

#### タイポグラフィスケール

```typescript
// components/ui/typography.tsx
import { cn } from "@/lib/utils"

export function H1({ className, ...props }: React.HTMLAttributes<HTMLHeadingElement>) {
  return (
    <h1
      className={cn(
        "scroll-m-20 text-4xl font-extrabold tracking-tight lg:text-5xl",
        className
      )}
      {...props}
    />
  )
}

export function H2({ className, ...props }: React.HTMLAttributes<HTMLHeadingElement>) {
  return (
    <h2
      className={cn(
        "scroll-m-20 border-b pb-2 text-3xl font-semibold tracking-tight first:mt-0",
        className
      )}
      {...props}
    />
  )
}

export function H3({ className, ...props }: React.HTMLAttributes<HTMLHeadingElement>) {
  return (
    <h3
      className={cn(
        "scroll-m-20 text-2xl font-semibold tracking-tight",
        className
      )}
      {...props}
    />
  )
}

export function H4({ className, ...props }: React.HTMLAttributes<HTMLHeadingElement>) {
  return (
    <h4
      className={cn(
        "scroll-m-20 text-xl font-semibold tracking-tight",
        className
      )}
      {...props}
    />
  )
}

export function P({ className, ...props }: React.HTMLAttributes<HTMLParagraphElement>) {
  return (
    <p
      className={cn("leading-7 [&:not(:first-child)]:mt-6", className)}
      {...props}
    />
  )
}

export function BlockQuote({ className, ...props }: React.HTMLAttributes<HTMLQuoteElement>) {
  return (
    <blockquote
      className={cn(
        "mt-6 border-l-2 border-primary-500 pl-6 italic",
        className
      )}
      {...props}
    />
  )
}

export function Code({ className, ...props }: React.HTMLAttributes<HTMLElement>) {
  return (
    <code
      className={cn(
        "relative rounded bg-neutral-100 px-[0.3rem] py-[0.2rem] font-mono text-sm",
        className
      )}
      {...props}
    />
  )
}

export function Lead({ className, ...props }: React.HTMLAttributes<HTMLParagraphElement>) {
  return (
    <p
      className={cn("text-xl text-neutral-600 dark:text-neutral-400", className)}
      {...props}
    />
  )
}

export function Large({ className, ...props }: React.HTMLAttributes<HTMLDivElement>) {
  return (
    <div
      className={cn("text-lg font-semibold", className)}
      {...props}
    />
  )
}

export function Small({ className, ...props }: React.HTMLAttributes<HTMLDivElement>) {
  return (
    <small
      className={cn("text-sm font-medium leading-none", className)}
      {...props}
    />
  )
}

export function Subtle({ className, ...props }: React.HTMLAttributes<HTMLParagraphElement>) {
  return (
    <p
      className={cn("text-sm text-neutral-500 dark:text-neutral-400", className)}
      {...props}
    />
  )
}
```

### 2.3 アイコノグラフィ

HRX-AIはLucide Iconsをメインのアイコンライブラリとして使用し、以下の原則に従います

- アイコンサイズの標準化（16px, 20px, 24px）
- 一貫した視覚的重みとスタイル
- セマンティックな使用（同じアクションに同じアイコン）

```tsx
// Example icon usage
import { 
  User, 
  Users, 
  Building, 
  BarChart, 
  FileText, 
  Calendar, 
  Settings 
} from "lucide-react"

// 標準サイズ
<User className="h-4 w-4" /> // 16px (小)
<User className="h-5 w-5" /> // 20px (中)
<User className="h-6 w-6" /> // 24px (大)

// コンテキストに応じた色
<AlertCircle className="h-4 w-4 text-danger-500" />
<CheckCircle className="h-4 w-4 text-success-500" />
<InfoIcon className="h-4 w-4 text-info-500" />
```

### 2.4 スペーシングシステム

Tailwind CSSのスペーシングシステムを使用します。8px単位のスケールを基本とし、最小4pxから使用します。

```typescript
// tailwind.config.js
const spacing = {
  px: '1px',
  0: '0px',
  0.5: '0.125rem', // 2px
  1: '0.25rem',    // 4px
  1.5: '0.375rem', // 6px
  2: '0.5rem',     // 8px
  2.5: '0.625rem', // 10px
  3: '0.75rem',    // 12px
  3.5: '0.875rem', // 14px
  4: '1rem',       // 16px
  5: '1.25rem',    // 20px
  6: '1.5rem',     // 24px
  7: '1.75rem',    // 28px
  8: '2rem',       // 32px
  9: '2.25rem',    // 36px
  10: '2.5rem',    // 40px
  11: '2.75rem',   // 44px
  12: '3rem',      // 48px
  14: '3.5rem',    // 56px
  16: '4rem',      // 64px
  20: '5rem',      // 80px
  24: '6rem',      // 96px
  28: '7rem',      // 112px
  32: '8rem',      // 128px
  36: '9rem',      // 144px
  40: '10rem',     // 160px
  44: '11rem',     // 176px
  48: '12rem',     // 192px
  52: '13rem',     // 208px
  56: '14rem',     // 224px
  60: '15rem',     // 240px
  64: '16rem',     // 256px
  72: '18rem',     // 288px
  80: '20rem',     // 320px
  96: '24rem',     // 384px
}
```

## 3. コンポーネントライブラリ

HRX-AIのUIコンポーネントは、Shadcn/UIを基盤として構築されています。すべてのコンポーネントは、アクセシビリティ、再利用性、カスタマイズ性を念頭に置いて設計されています。

### 3.1 基本コンポーネント

#### Button

アクション要素として使用されるボタンコンポーネントです。

```tsx
import { Button } from "@/components/ui/button"

// 標準ボタン
<Button>ボタンテキスト</Button>

// バリエーション
<Button variant="default">デフォルト</Button>
<Button variant="destructive">削除</Button>
<Button variant="outline">アウトライン</Button>
<Button variant="secondary">セカンダリ</Button>
<Button variant="ghost">ゴースト</Button>
<Button variant="link">リンク</Button>

// サイズ
<Button size="default">デフォルト</Button>
<Button size="sm">スモール</Button>
<Button size="lg">ラージ</Button>
<Button size="icon"><Plus className="h-4 w-4" /></Button>

// 状態
<Button disabled>無効</Button>
<Button isLoading>読み込み中</Button>
<Button asChild><a href="/dashboard">リンクとして</a></Button>
```

#### Badge

情報のカテゴリ、ステータス、またはタグを表示するコンポーネントです。

```tsx
import { Badge } from "@/components/ui/badge"

// 標準バッジ
<Badge>新規</Badge>

// バリエーション
<Badge variant="default">デフォルト</Badge>
<Badge variant="secondary">セカンダリ</Badge>
<Badge variant="outline">アウトライン</Badge>
<Badge variant="destructive">デストラクティブ</Badge>

// カスタムバッジ
<Badge variant="outline" className="bg-info-50 text-info-600 border-info-200">
  情報
</Badge>
<Badge variant="outline" className="bg-success-50 text-success-600 border-success-200">
  成功
</Badge>
<Badge variant="outline" className="bg-warning-50 text-warning-600 border-warning-200">
  警告
</Badge>
```

#### Card

構造化された情報ブロックを表示するカードコンポーネントです。

```tsx
import {
  Card,
  CardContent,
  CardDescription,
  CardFooter,
  CardHeader,
  CardTitle,
} from "@/components/ui/card"
import { Button } from "@/components/ui/button"

<Card>
  <CardHeader>
    <CardTitle>従業員情報</CardTitle>
    <CardDescription>従業員の基本データと業績指標</CardDescription>
  </CardHeader>
  <CardContent>
    <p>ここにコンテンツが入ります。</p>
  </CardContent>
  <CardFooter>
    <Button>詳細を見る</Button>
  </CardFooter>
</Card>
```

#### Avatar

ユーザーのプロフィール画像や、イニシャルを表示するコンポーネントです。

```tsx
import {
  Avatar,
  AvatarFallback,
  AvatarImage,
} from "@/components/ui/avatar"

<Avatar>
  <AvatarImage src="https://github.com/shadcn.png" alt="@shadcn" />
  <AvatarFallback>CN</AvatarFallback>
</Avatar>
```

### 3.2 フォーム要素

#### Input

テキスト入力フィールドです。

```tsx
import { Input } from "@/components/ui/input"

// 標準入力
<Input type="text" placeholder="名前を入力" />

// バリエーション
<Input type="email" placeholder="メールアドレス" />
<Input type="password" placeholder="パスワード" />
<Input type="number" placeholder="年齢" />
<Input type="search" placeholder="検索..." />

// 状態
<Input disabled placeholder="入力不可" />
<Input aria-invalid="true" className="border-danger-500" placeholder="エラー状態" />
```

#### Select

ドロップダウン選択肢を提供するコンポーネントです。

```tsx
import {
  Select,
  SelectContent,
  SelectGroup,
  SelectItem,
  SelectLabel,
  SelectTrigger,
  SelectValue,
} from "@/components/ui/select"

<Select>
  <SelectTrigger className="w-[180px]">
    <SelectValue placeholder="部門を選択" />
  </SelectTrigger>
  <SelectContent>
    <SelectGroup>
      <SelectLabel>部門</SelectLabel>
      <SelectItem value="engineering">エンジニアリング</SelectItem>
      <SelectItem value="sales">営業</SelectItem>
      <SelectItem value="marketing">マーケティング</SelectItem>
      <SelectItem value="hr">人事</SelectItem>
    </SelectGroup>
  </SelectContent>
</Select>
```

#### Checkbox

チェックボックス入力要素です。

```tsx
import { Checkbox } from "@/components/ui/checkbox"

<div className="flex items-center space-x-2">
  <Checkbox id="terms" />
  <label
    htmlFor="terms"
    className="text-sm font-medium leading-none peer-disabled:cursor-not-allowed peer-disabled:opacity-70"
  >
    利用規約に同意する
  </label>
</div>
```

#### RadioGroup

相互に排他的な選択肢を提供するラジオボタングループです。

```tsx
import { Label } from "@/components/ui/label"
import { RadioGroup, RadioGroupItem } from "@/components/ui/radio-group"

<RadioGroup defaultValue="option-one">
  <div className="flex items-center space-x-2">
    <RadioGroupItem value="option-one" id="option-one" />
    <Label htmlFor="option-one">オプション 1</Label>
  </div>
  <div className="flex items-center space-x-2">
    <RadioGroupItem value="option-two" id="option-two" />
    <Label htmlFor="option-two">オプション 2</Label>
  </div>
</RadioGroup>
```

#### Switch

トグルスイッチ要素です。

```tsx
import { Label } from "@/components/ui/label"
import { Switch } from "@/components/ui/switch"

<div className="flex items-center space-x-2">
  <Switch id="airplane-mode" />
  <Label htmlFor="airplane-mode">通知を有効にする</Label>
</div>
```

#### DatePicker

日付選択コンポーネントです。

```tsx
import { format } from "date-fns"
import { Calendar as CalendarIcon } from "lucide-react"
import { cn } from "@/lib/utils"
import { Button } from "@/components/ui/button"
import { Calendar } from "@/components/ui/calendar"
import {
  Popover,
  PopoverContent,
  PopoverTrigger,
} from "@/components/ui/popover"
import { useState } from "react"

export function DatePicker() {
  const [date, setDate] = useState<Date>()

  return (
    <Popover>
      <PopoverTrigger asChild>
        <Button
          variant={"outline"}
          className={cn(
            "w-[240px] justify-start text-left font-normal",
            !date && "text-muted-foreground"
          )}
        >
          <CalendarIcon className="mr-2 h-4 w-4" />
          {date ? format(date, "PPP") : <span>日付を選択</span>}
        </Button>
      </PopoverTrigger>
      <PopoverContent className="w-auto p-0" align="start">
        <Calendar
          mode="single"
          selected={date}
          onSelect={setDate}
          initialFocus
        />
      </PopoverContent>
    </Popover>
  )
}
```

### 3.3 ナビゲーション

#### NavigationMenu

アプリケーションのメインナビゲーションを提供するコンポーネントです。

```tsx
import {
  NavigationMenu,
  NavigationMenuContent,
  NavigationMenuItem,
  NavigationMenuLink,
  NavigationMenuList,
  NavigationMenuTrigger,
} from "@/components/ui/navigation-menu"
import { cn } from "@/lib/utils"
import Link from "next/link"

export function MainNav() {
  return (
    <NavigationMenu>
      <NavigationMenuList>
        <NavigationMenuItem>
          <NavigationMenuTrigger>ダッシュボード</NavigationMenuTrigger>
          <NavigationMenuContent>
            <ul className="grid gap-3 p-4 md:w-[400px] lg:w-[500px] lg:grid-cols-[.75fr_1fr]">
              <li className="row-span-3">
                <NavigationMenuLink asChild>
                  <a
                    className="flex h-full w-full select-none flex-col justify-end rounded-md bg-gradient-to-b from-primary-500 to-primary-700 p-6 no-underline outline-none focus:shadow-md"
                    href="/"
                  >
                    <div className="mb-2 mt-4 text-lg font-medium text-white">
                      HRX-AI
                    </div>
                    <p className="text-sm leading-tight text-white/90">
                      次世代スーパー人事AIエージェント
                    </p>
                  </a>
                </NavigationMenuLink>
              </li>
              <li>
                <NavigationMenuLink asChild>
                  <Link href="/dashboard/overview" className={cn(
                    "block select-none space-y-1 rounded-md p-3 leading-none no-underline outline-none transition-colors hover:bg-neutral-100 focus:bg-neutral-100"
                  )}>
                    <div className="text-sm font-medium leading-none">概要</div>
                    <p className="line-clamp-2 text-sm leading-snug text-neutral-500">
                      主要KPIと組織全体の概況を表示
                    </p>
                  </Link>
                </NavigationMenuLink>
              </li>
              <li>
                <NavigationMenuLink asChild>
                  <Link href="/dashboard/analytics" className={cn(
                    "block select-none space-y-1 rounded-md p-3 leading-none no-underline outline-none transition-colors hover:bg-neutral-100 focus:bg-neutral-100"
                  )}>
                    <div className="text-sm font-medium leading-none">分析</div>
                    <p className="line-clamp-2 text-sm leading-snug text-neutral-500">
                      詳細なデータ分析とインサイト
                    </p>
                  </Link>
                </NavigationMenuLink>
              </li>
              <li>
                <NavigationMenuLink asChild>
                  <Link href="/dashboard/reports" className={cn(
                    "block select-none space-y-1 rounded-md p-3 leading-none no-underline outline-none transition-colors hover:bg-neutral-100 focus:bg-neutral-100"
                  )}>
                    <div className="text-sm font-medium leading-none">レポート</div>
                    <p className="line-clamp-2 text-sm leading-snug text-neutral-500">
                      カスタムレポートの生成と管理
                    </p>
                  </Link>
                </NavigationMenuLink>
              </li>
            </ul>
          </NavigationMenuContent>
        </NavigationMenuItem>
        <NavigationMenuItem>
          <NavigationMenuTrigger>従業員</NavigationMenuTrigger>
          <NavigationMenuContent>
            <ul className="grid w-[400px] gap-3 p-4 md:w-[500px] md:grid-cols-2 lg:w-[600px]">
              <li>
                <NavigationMenuLink asChild>
                  <Link href="/employees" className={cn(
                    "block select-none space-y-1 rounded-md p-3 leading-none no-underline outline-none transition-colors hover:bg-neutral-100 focus:bg-neutral-100"
                  )}>
                    <div className="text-sm font-medium leading-none">従業員一覧</div>
                    <p className="line-clamp-2 text-sm leading-snug text-neutral-500">
                      組織内のすべての従業員を表示
                    </p>
                  </Link>
                </NavigationMenuLink>
              </li>
              <li>
                <NavigationMenuLink asChild>
                  <Link href="/employees/performance" className={cn(
                    "block select-none space-y-1 rounded-md p-3 leading-none no-underline outline-none transition-colors hover:bg-neutral-100 focus:bg-neutral-100"
                  )}>
                    <div className="text-sm font-medium leading-none">パフォーマンス評価</div>
                    <p className="line-clamp-2 text-sm leading-snug text-neutral-500">
                      評価サイクルと従業員パフォーマンス
                    </p>
                  </Link>
                </NavigationMenuLink>
              </li>
              <li>
                <NavigationMenuLink asChild>
                  <Link href="/employees/skills" className={cn(
                    "block select-none space-y-1 rounded-md p-3 leading-none no-underline outline-none transition-colors hover:bg-neutral-100 focus:bg-neutral-100"
                  )}>
                    <div className="text-sm font-medium leading-none">スキルマネジメント</div>
                    <p className="line-clamp-2 text-sm leading-snug text-neutral-500">
                      スキルマトリクスとギャップ分析
                    </p>
                  </Link>
                </NavigationMenuLink>
              </li>
              <li>
                <NavigationMenuLink asChild>
                  <Link href="/employees/engagement" className={cn(
                    "block select-none space-y-1 rounded-md p-3 leading-none no-underline outline-none transition-colors hover:bg-neutral-100 focus:bg-neutral-100"
                  )}>
                    <div className="text-sm font-medium leading-none">エンゲージメント</div>
                    <p className="line-clamp-2 text-sm leading-snug text-neutral-500">
                      従業員エンゲージメント分析
                    </p>
                  </Link>
                </NavigationMenuLink>
              </li>
            </ul>
          </NavigationMenuContent>
        </NavigationMenuItem>
        <NavigationMenuItem>
          <NavigationMenuTrigger>採用</NavigationMenuTrigger>
          <NavigationMenuContent>
            <ul className="grid w-[400px] gap-3 p-4 md:w-[500px] md:grid-cols-2 lg:w-[600px]">
              <li>
                <NavigationMenuLink asChild>
                  <Link href="/recruitment/jobs" className={cn(
                    "block select-none space-y-1 rounded-md p-3 leading-none no-underline outline-none transition-colors hover:bg-neutral-100 focus:bg-neutral-100"
                  )}>
                    <div className="text-sm font-medium leading-none">求人管理</div>
                    <p className="line-clamp-2 text-sm leading-snug text-neutral-500">
                      求人ポジションの作成と管理
                    </p>
                  </Link>
                </NavigationMenuLink>
              </li>
              <li>
                <NavigationMenuLink asChild>
                  <Link href="/recruitment/candidates" className={cn(
                    "block select-none space-y-1 rounded-md p-3 leading-none no-underline outline-none transition-colors hover:bg-neutral-100 focus:bg-neutral-100"
                  )}>
                    <div className="text-sm font-medium leading-none">候補者管理</div>
                    <p className="line-clamp-2 text-sm leading-snug text-neutral-500">
                      応募者のスクリーニングと評価
                    </p>
                  </Link>
                </NavigationMenuLink>
              </li>
              <li>
                <NavigationMenuLink asChild>
                  <Link href="/recruitment/interviews" className={cn(
                    "block select-none space-y-1 rounded-md p-3 leading-none no-underline outline-none transition-colors hover:bg-neutral-100 focus:bg-neutral-100"
                  )}>
                    <div className="text-sm font-medium leading-none">面接管理</div>
                    <p className="line-clamp-2 text-sm leading-snug text-neutral-500">
                      面接のスケジュールと評価
                    </p>
                  </Link>
                </NavigationMenuLink>
              </li>
              <li>
                <NavigationMenuLink asChild>
                  <Link href="/recruitment/analytics" className={cn(
                    "block select-none space-y-1 rounded-md p-3 leading-none no-underline outline-none transition-colors hover:bg-neutral-100 focus:bg-neutral-100"
                  )}>
                    <div className="text-sm font-medium leading-none">採用分析</div>
                    <p className="line-clamp-2 text-sm leading-snug text-neutral-500">
                      採用パフォーマンスとROI分析
                    </p>
                  </Link>
                </NavigationMenuLink>
              </li>
            </ul>
          </NavigationMenuContent>
        </NavigationMenuItem>
        <NavigationMenuItem>
          <Link href="/settings" legacyBehavior passHref>
            <NavigationMenuLink className={cn(
              "group inline-flex h-10 w-max items-center justify-center rounded-md bg-background px-4 py-2 text-sm font-medium transition-colors hover:bg-neutral-100 hover:text-neutral-900 focus:bg-neutral-100 focus:text-neutral-900 focus:outline-none disabled:pointer-events-none disabled:opacity-50 data-[active]:bg-neutral-100/50 data-[state=open]:bg-neutral-100/50"
            )}>
              設定
            </NavigationMenuLink>
          </Link>
        </NavigationMenuItem>
      </NavigationMenuList>
    </NavigationMenu>
  )
}
```

#### Tabs

タブベースのナビゲーションを提供するコンポーネントです。

```tsx
import { Tabs, TabsContent, TabsList, TabsTrigger } from "@/components/ui/tabs"

<Tabs defaultValue="overview" className="w-full">
  <TabsList className="grid w-full grid-cols-3">
    <TabsTrigger value="overview">概要</TabsTrigger>
    <TabsTrigger value="performance">パフォーマンス</TabsTrigger>
    <TabsTrigger value="engagement">エンゲージメント</TabsTrigger>
  </TabsList>
  <TabsContent value="overview">
    <Card>
      <CardHeader>
        <CardTitle>概要</CardTitle>
        <CardDescription>主要指標と概要情報</CardDescription>
      </CardHeader>
      <CardContent className="space-y-2">
        コンテンツが入ります
      </CardContent>
    </Card>
  </TabsContent>
  <TabsContent value="performance">
    <Card>
      <CardHeader>
        <CardTitle>パフォーマンス</CardTitle>
        <CardDescription>パフォーマンス評価と履歴</CardDescription>
      </CardHeader>
      <CardContent className="space-y-2">
        コンテンツが入ります
      </CardContent>
    </Card>
  </TabsContent>
  <TabsContent value="engagement">
    <Card>
      <CardHeader>
        <CardTitle>エンゲージメント</CardTitle>
        <CardDescription>従業員エンゲージメント指標</CardDescription>
      </CardHeader>
      <CardContent className="space-y-2">
        コンテンツが入ります
      </CardContent>
    </Card>
  </TabsContent>
</Tabs>
```

#### Breadcrumb

現在の場所をサイト階層で表示するパンくずリストコンポーネントです。

```tsx
import {
  Breadcrumb,
  BreadcrumbItem,
  BreadcrumbLink,
  BreadcrumbList,
  BreadcrumbPage,
  BreadcrumbSeparator,
} from "@/components/ui/breadcrumb"

<Breadcrumb>
  <BreadcrumbList>
    <BreadcrumbItem>
      <BreadcrumbLink href="/">ホーム</BreadcrumbLink>
    </BreadcrumbItem>
    <BreadcrumbSeparator />
    <BreadcrumbItem>
      <BreadcrumbLink href="/employees">従業員</BreadcrumbLink>
    </BreadcrumbItem>
    <BreadcrumbSeparator />
    <BreadcrumbItem>
      <BreadcrumbPage>山田太郎</BreadcrumbPage>
    </BreadcrumbItem>
  </BreadcrumbList>
</Breadcrumb>
```

#### Pagination

長いリストやテーブルのページネーションを提供するコンポーネントです。

```tsx
import {
  Pagination,
  PaginationContent,
  PaginationItem,
  PaginationLink,
  PaginationNext,
  PaginationPrevious,
} from "@/components/ui/pagination"

<Pagination>
  <PaginationContent>
    <PaginationItem>
      <PaginationPrevious href="#" />
    </PaginationItem>
    <PaginationItem>
      <PaginationLink href="#">1</PaginationLink>
    </PaginationItem>
    <PaginationItem>
      <PaginationLink href="#" isActive>2</PaginationLink>
    </PaginationItem>
    <PaginationItem>
      <PaginationLink href="#">3</PaginationLink>
    </PaginationItem>
    <PaginationItem>
      <PaginationNext href="#" />
    </PaginationItem>
  </PaginationContent>
</Pagination>
```

### 3.4 データ表示

#### Table

データテーブル表示コンポーネントです。

```tsx
import {
  Table,
  TableBody,
  TableCaption,
  TableCell,
  TableHead,
  TableHeader,
  TableRow,
} from "@/components/ui/table"

<Table>
  <TableCaption>従業員リスト</TableCaption>
  <TableHeader>
    <TableRow>
      <TableHead className="w-[100px]">ID</TableHead>
      <TableHead>名前</TableHead>
      <TableHead>部署</TableHead>
      <TableHead>入社日</TableHead>
      <TableHead className="text-right">パフォーマンス</TableHead>
    </TableRow>
  </TableHeader>
  <TableBody>
    <TableRow>
      <TableCell className="font-medium">EMP001</TableCell>
      <TableCell>山田太郎</TableCell>
      <TableCell>営業部</TableCell>
      <TableCell>2022/04/01</TableCell>
      <TableCell className="text-right">8.5/10</TableCell>
    </TableRow>
    <TableRow>
      <TableCell className="font-medium">EMP002</TableCell>
      <TableCell>佐藤花子</TableCell>
      <TableCell>マーケティング部</TableCell>
      <TableCell>2021/10/15</TableCell>
      <TableCell className="text-right">9.2/10</TableCell>
    </TableRow>
  </TableBody>
</Table>
```

#### DataTable

高度なソート、フィルタリング、ページネーション機能を持つデータテーブルコンポーネントです。

```tsx
// components/ui/data-table.tsx
"use client"

import {
  ColumnDef,
  flexRender,
  getCoreRowModel,
  useReactTable,
  getPaginationRowModel,
  SortingState,
  getSortedRowModel,
  ColumnFiltersState,
  getFilteredRowModel,
} from "@tanstack/react-table"

import {
  Table,
  TableBody,
  TableCell,
  TableHead,
  TableHeader,
  TableRow,
} from "@/components/ui/table"
import { Button } from "@/components/ui/button"
import { Input } from "@/components/ui/input"
import React from "react"

interface DataTableProps<TData, TValue> {
  columns: ColumnDef<TData, TValue>[]
  data: TData[]
  searchKey?: string
}

export function DataTable<TData, TValue>({
  columns,
  data,
  searchKey,
}: DataTableProps<TData, TValue>) {
  const [sorting, setSorting] = React.useState<SortingState>([])
  const [columnFilters, setColumnFilters] = React.useState<ColumnFiltersState>([])

  const table = useReactTable({
    data,
    columns,
    getCoreRowModel: getCoreRowModel(),
    getPaginationRowModel: getPaginationRowModel(),
    onSortingChange: setSorting,
    getSortedRowModel: getSortedRowModel(),
    onColumnFiltersChange: setColumnFilters,
    getFilteredRowModel: getFilteredRowModel(),
    state: {
      sorting,
      columnFilters,
    },
  })

  return (
    <div>
      {searchKey && (
        <div className="flex items-center py-4">
          <Input
            placeholder="検索..."
            value={(table.getColumn(searchKey)?.getFilterValue() as string) ?? ""}
            onChange={(event) =>
              table.getColumn(searchKey)?.setFilterValue(event.target.value)
            }
            className="max-w-sm"
          />
        </div>
      )}
      <div className="rounded-md border">
        <Table>
          <TableHeader>
            {table.getHeaderGroups().map((headerGroup) => (
              <TableRow key={headerGroup.id}>
                {headerGroup.headers.map((header) => {
                  return (
                    <TableHead key={header.id}>
                      {header.isPlaceholder
                        ? null
                        : flexRender(
                            header.column.columnDef.header,
                            header.getContext()
                          )}
                    </TableHead>
                  )
                })}
              </TableRow>
            ))}
          </TableHeader>
          <TableBody>
            {table.getRowModel().rows?.length ? (
              table.getRowModel().rows.map((row) => (
                <TableRow
                  key={row.id}
                  data-state={row.getIsSelected() && "selected"}
                >
                  {row.getVisibleCells().map((cell) => (
                    <TableCell key={cell.id}>
                      {flexRender(cell.column.columnDef.cell, cell.getContext())}
                    </TableCell>
                  ))}
                </TableRow>
              ))
            ) : (
              <TableRow>
                <TableCell colSpan={columns.length} className="h-24 text-center">
                  データがありません
                </TableCell>
              </TableRow>
            )}
          </TableBody>
        </Table>
      </div>
      <div className="flex items-center justify-end space-x-2 py-4">
        <Button
          variant="outline"
          size="sm"
          onClick={() => table.previousPage()}
          disabled={!table.getCanPreviousPage()}
        >
          前へ
        </Button>
        <Button
          variant="outline"
          size="sm"
          onClick={() => table.nextPage()}
          disabled={!table.getCanNextPage()}
        >
          次へ
        </Button>
      </div>
    </div>
  )
}
```

#### Chart

データ可視化を行うチャートコンポーネントです。

```tsx
// components/ui/chart.tsx
"use client"

import { ResponsiveLine } from "@nivo/line"
import { ResponsiveBar } from "@nivo/bar"
import { ResponsivePie } from "@nivo/pie"
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card"

interface ChartProps {
  data: any[]
  title: string
  type: "line" | "bar" | "pie"
  height?: number
  isLoading?: boolean
}

export function Chart({ data, title, type, height = 300, isLoading }: ChartProps) {
  if (isLoading) {
    return (
      <Card>
        <CardHeader>
          <CardTitle>{title}</CardTitle>
        </CardHeader>
        <CardContent>
          <div className="h-[300px] w-full animate-pulse bg-neutral-200" />
        </CardContent>
      </Card>
    )
  }

  return (
    <Card>
      <CardHeader>
        <CardTitle>{title}</CardTitle>
      </CardHeader>
      <CardContent>
        <div style={{ height: height }}>
          {type === 'line' && (
            <ResponsiveLine
              data={data}
              margin={{ top: 20, right: 20, bottom: 50, left: 50 }}
              xScale={{ type: 'point' }}
              yScale={{ type: 'linear', min: 'auto', max: 'auto', stacked: false, reverse: false }}
              axisBottom={{
                tickSize: 5,
                tickPadding: 5,
                tickRotation: 0,
                legend: 'トランスポート',
                legendOffset: 36,
                legendPosition: 'middle'
              }}
              axisLeft={{
                tickSize: 5,
                tickPadding: 5,
                tickRotation: 0,
                legend: '数',
                legendOffset: -40,
                legendPosition: 'middle'
              }}
              colors={{ scheme: 'category10' }}
              enablePoints={true}
              pointSize={10}
              pointColor={{ theme: 'background' }}
              pointBorderWidth={2}
              pointBorderColor={{ from: 'serieColor' }}
              enableCrosshair={false}
              useMesh={true}
            />
          )}
          {type === 'bar' && (
            <ResponsiveBar
              data={data}
              keys={['value']}
              indexBy="id"
              margin={{ top: 20, right: 20, bottom: 50, left: 50 }}
              padding={0.3}
              colors={{ scheme: 'category10' }}
              axisBottom={{
                tickSize: 5,
                tickPadding: 5,
                tickRotation: 0,
                legend: 'カテゴリ',
                legendPosition: 'middle',
                legendOffset: 32
              }}
              axisLeft={{
                tickSize: 5,
                tickPadding: 5,
                tickRotation: 0,
                legend: '値',
                legendPosition: 'middle',
                legendOffset: -40
              }}
              labelSkipWidth={12}
              labelSkipHeight={12}
              animate={true}
            />
          )}
          {type === 'pie' && (
            <ResponsivePie
              data={data}
              margin={{ top: 20, right: 20, bottom: 20, left: 20 }}
              innerRadius={0.5}
              padAngle={0.7}
              cornerRadius={3}
              colors={{ scheme: 'category10' }}
              borderWidth={1}
              borderColor={{ from: 'color', modifiers: [['darker', 0.2]] }}
              radialLabelsSkipAngle={10}
              radialLabelsTextXOffset={6}
              radialLabelsTextColor="#333333"
              radialLabelsLinkOffset={0}
              radialLabelsLinkDiagonalLength={16}
              radialLabelsLinkHorizontalLength={24}
              radialLabelsLinkStrokeWidth={1}
              radialLabelsLinkColor={{ from: 'color' }}
              slicesLabelsSkipAngle={10}
              slicesLabelsTextColor="#333333"
              animate={true}
            />
          )}
        </div>
      </CardContent>
    </Card>
  )
}
```

#### Calendar

カレンダーコンポーネントです。

```tsx
import { Calendar } from "@/components/ui/calendar"
import { useState } from "react"

export function CalendarDemo() {
  const [date, setDate] = useState<Date | undefined>(new Date())

  return (
    <Calendar
      mode="single"
      selected={date}
      onSelect={setDate}
      className="rounded-md border"
    />
  )
}
```

### 3.5 フィードバック

#### Alert

ユーザーに重要な情報を通知するアラートコンポーネントです。

```tsx
import {
  Alert,
  AlertDescription,
  AlertTitle,
} from "@/components/ui/alert"
import { AlertCircle, Info, CheckCircle } from "lucide-react"

// 情報アラート
<Alert>
  <Info className="h-4 w-4" />
  <AlertTitle>情報</AlertTitle>
  <AlertDescription>
    プロファイルが更新されました
  </AlertDescription>
</Alert>

// 成功アラート
<Alert className="bg-success-50 text-success-700 border-success-200">
  <CheckCircle className="h-4 w-4" />
  <AlertTitle>成功</AlertTitle>
  <AlertDescription>
    変更が正常に保存されました
  </AlertDescription>
</Alert>

// 警告アラート
<Alert variant="destructive">
  <AlertCircle className="h-4 w-4" />
  <AlertTitle>エラー</AlertTitle>
  <AlertDescription>
    エラーが発生しました。もう一度お試しください。
  </AlertDescription>
</Alert>
```

#### Toast

一時的な通知を表示するトーストコンポーネントです。

```tsx
import { useToast } from "@/components/ui/use-toast"
import { Button } from "@/components/ui/button"

export function ToastDemo() {
  const { toast } = useToast()

  return (
    <Button
      onClick={() => {
        toast({
          title: "スケジュールされました",
          description: "金曜日の午後1時に面接が設定されました",
        })
      }}
    >
      通知を表示
    </Button>
  )
}
```

#### Progress

進行状況を表示するプログレスバーコンポーネントです。

```tsx
import { Progress } from "@/components/ui/progress"

// 基本的なプログレスバー
<Progress value={33} />

// カラーバリエーション
<Progress 
  value={66} 
  className="bg-info-100 text-info-500"
  indicatorClassName="bg-info-500"
/>

// ラベル付きプログレスバー
<div className="space-y-1">
  <div className="flex justify-between text-sm">
    <span>進行中...</span>
    <span>66%</span>
  </div>
  <Progress value={66} />
</div>
```

### 3.6 オーバーレイ

#### Dialog

モーダルダイアログコンポーネントです。

```tsx
import {
  Dialog,
  DialogContent,
  DialogDescription,
  DialogFooter,
  DialogHeader,
  DialogTitle,
  DialogTrigger,
} from "@/components/ui/dialog"
import { Button } from "@/components/ui/button"
import { Input } from "@/components/ui/input"
import { Label } from "@/components/ui/label"

<Dialog>
  <DialogTrigger asChild>
    <Button variant="outline">従業員を編集</Button>
  </DialogTrigger>
  <DialogContent className="sm:max-w-[425px]">
    <DialogHeader>
      <DialogTitle>従業員プロファイル編集</DialogTitle>
      <DialogDescription>
        従業員情報を更新します。完了したら保存を押してください。
      </DialogDescription>
    </DialogHeader>
    <div className="grid gap-4 py-4">
      <div className="grid grid-cols-4 items-center gap-4">
        <Label htmlFor="name" className="text-right">
          名前
        </Label>
        <Input id="name" value="山田太郎" className="col-span-3" />
      </div>
      <div className="grid grid-cols-4 items-center gap-4">
        <Label htmlFor="position" className="text-right">
          役職
        </Label>
        <Input id="position" value="マネージャー" className="col-span-3" />
      </div>
    </div>
    <DialogFooter>
      <Button type="submit">保存</Button>
    </DialogFooter>
  </DialogContent>
</Dialog>
```

#### Drawer

サイドから表示されるドロワーコンポーネントです。

```tsx
import { Button } from "@/components/ui/button"
import {
  Drawer,
  DrawerClose,
  DrawerContent,
  DrawerDescription,
  DrawerFooter,
  DrawerHeader,
  DrawerTitle,
  DrawerTrigger,
} from "@/components/ui/drawer"

<Drawer>
  <DrawerTrigger asChild>
    <Button variant="outline">詳細を表示</Button>
  </DrawerTrigger>
  <DrawerContent>
    <div className="mx-auto w-full max-w-sm">
      <DrawerHeader>
        <DrawerTitle>従業員詳細</DrawerTitle>
        <DrawerDescription>従業員の詳細情報を表示します</DrawerDescription>
      </DrawerHeader>
      <div className="p-4 pb-0">
        <div className="space-y-4">
          <div>
            <h4 className="text-sm font-medium">基本情報</h4>
            <p className="text-sm text-neutral-500">名前: 山田太郎</p>
            <p className="text-sm text-neutral-500">部署: 営業部</p>
            <p className="text-sm text-neutral-500">入社日: 2022/04/01</p>
          </div>
          <div>
            <h4 className="text-sm font-medium">スキル</h4>
            <div className="flex flex-wrap gap-1 mt-1">
              <Badge>セールス</Badge>
              <Badge>プレゼンテーション</Badge>
              <Badge>顧客管理</Badge>
            </div>
          </div>
        </div>
      </div>
      <DrawerFooter>
        <Button>編集</Button>
        <DrawerClose asChild>
          <Button variant="outline">閉じる</Button>
        </DrawerClose>
      </DrawerFooter>
    </div>
  </DrawerContent>
</Drawer>
```

#### HoverCard

要素にホバーしたときに表示される追加情報カードです。

```tsx
import {
  HoverCard,
  HoverCardContent,
  HoverCardTrigger,
} from "@/components/ui/hover-card"
import { Avatar, AvatarFallback, AvatarImage } from "@/components/ui/avatar"
import { CalendarDays } from "lucide-react"

<HoverCard>
  <HoverCardTrigger asChild>
    <Button variant="link">山田太郎</Button>
  </HoverCardTrigger>
  <HoverCardContent className="w-80">
    <div className="flex justify-between space-x-4">
      <Avatar>
        <AvatarImage src="https://github.com/shadcn.png" />
        <AvatarFallback>YT</AvatarFallback>
      </Avatar>
      <div className="space-y-1">
        <h4 className="text-sm font-semibold">山田太郎</h4>
        <p className="text-sm">
          営業部 / シニアセールスマネージャー
        </p>
        <div className="flex items-center pt-2">
          <CalendarDays className="mr-2 h-4 w-4 opacity-70" />
          <span className="text-xs text-neutral-500">
            2022年4月1日に入社
          </span>
        </div>
      </div>
    </div>
  </HoverCardContent>
</HoverCard>
```

#### DropdownMenu

ドロップダウンメニューコンポーネントです。

```tsx
import {
  DropdownMenu,
  DropdownMenuContent,
  DropdownMenuItem,
  DropdownMenuLabel,
  DropdownMenuSeparator,
  DropdownMenuTrigger,
} from "@/components/ui/dropdown-menu"
import { Button } from "@/components/ui/button"
import { MoreHorizontal } from "lucide-react"

<DropdownMenu>
  <DropdownMenuTrigger asChild>
    <Button variant="ghost" size="icon">
      <MoreHorizontal className="h-4 w-4" />
    </Button>
  </DropdownMenuTrigger>
  <DropdownMenuContent align="end">
    <DropdownMenuLabel>アクション</DropdownMenuLabel>
    <DropdownMenuItem>詳細を表示</DropdownMenuItem>
    <DropdownMenuItem>編集</DropdownMenuItem>
    <DropdownMenuSeparator />
    <DropdownMenuItem className="text-danger-500">削除</DropdownMenuItem>
  </DropdownMenuContent>
</DropdownMenu>
```

### 3.7 AIインターフェース

#### AIAssistant

AIアシスタントコンポーネントです。

```tsx
import { useState } from "react"
import { Button } from "@/components/ui/button"
import { Card, CardHeader, CardTitle, CardContent, CardFooter } from "@/components/ui/card"
import { Textarea } from "@/components/ui/textarea"
import { Avatar, AvatarFallback, AvatarImage } from "@/components/ui/avatar"
import { Send, Bot, User, Sparkles } from "lucide-react"

export function AIAssistant() {
  const [messages, setMessages] = useState([
    { role: "assistant", content: "こんにちは！HRX-AIアシスタントです。人事に関する質問や分析依頼があればお気軽にどうぞ。" }
  ]);
  const [input, setInput] = useState("");
  const [isLoading, setIsLoading] = useState(false);

  const handleSendMessage = async () => {
    if (!input.trim()) return;
    
    // ユーザーメッセージを追加
    const updatedMessages = [
      ...messages,
      { role: "user", content: input }
    ];
    setMessages(updatedMessages);
    setInput("");
    setIsLoading(true);
    
    // ここでAIリクエスト処理（サンプルとして単純な応答を使用）
    setTimeout(() => {
      setMessages([
        ...updatedMessages,
        { role: "assistant", content: "ありがとうございます。従業員の年間評価レポートを作成するお手伝いができます。評価期間と主要パフォーマンス指標を教えていただけますか？" }
      ]);
      setIsLoading(false);
    }, 1000);
  };

  return (
    <Card className="w-full max-w-3xl mx-auto">
      <CardHeader>
        <CardTitle className="flex items-center">
          <Sparkles className="h-5 w-5 mr-2 text-primary-500" />
          HRX-AIアシスタント
        </CardTitle>
      </CardHeader>
      <CardContent>
        <div className="space-y-4 max-h-[400px] overflow-y-auto p-1">
          {messages.map((message, index) => (
            <div key={index} className={`flex ${message.role === "user" ? "justify-end" : "justify-start"}`}>
              <div className={`flex gap-3 max-w-[80%] ${message.role === "user" ? "flex-row-reverse" : ""}`}>
                <Avatar className={`h-8 w-8 ${message.role === "assistant" ? "bg-primary-100" : "bg-accent-100"}`}>
                  {message.role === "assistant" ? (
                    <Bot className="h-5 w-5 text-primary-700" />
                  ) : (
                    <User className="h-5 w-5 text-accent-700" />
                  )}
                  <AvatarFallback>{message.role === "assistant" ? "AI" : "You"}</AvatarFallback>
                </Avatar>
                <div 
                  className={`rounded-lg p-3 ${message.role === "assistant" 
                    ? "bg-primary-50 text-neutral-800" 
                    : "bg-accent-50 text-neutral-800"}`
                  }
                >
                  {message.content}
                </div>
              </div>
            </div>
          ))}
          {isLoading && (
            <div className="flex justify-start">
              <div className="flex gap-3 max-w-[80%]">
                <Avatar className="h-8 w-8 bg-primary-100">
                  <Bot className="h-5 w-5 text-primary-700" />
                  <AvatarFallback>AI</AvatarFallback>
                </Avatar>
                <div className="rounded-lg p-4 bg-primary-50">
                  <div className="flex space-x-2">
                    <div className="h-2 w-2 rounded-full bg-primary-400 animate-bounce"></div>
                    <div className="h-2 w-2 rounded-full bg-primary-400 animate-bounce [animation-delay:0.2s]"></div>
                    <div className="h-2 w-2 rounded-full bg-primary-400 animate-bounce [animation-delay:0.4s]"></div>
                  </div>
                </div>
              </div>
            </div>
          )}
        </div>
      </CardContent>
      <CardFooter>
        <div className="flex w-full items-center space-x-2">
          <Textarea
            placeholder="質問を入力してください..."
            value={input}
            onChange={(e) => setInput(e.target.value)}
            className="flex-1"
            onKeyDown={(e) => {
              if (e.key === 'Enter' && !e.shiftKey) {
                e.preventDefault();
                handleSendMessage();
              }
            }}
          />
          <Button 
            onClick={handleSendMessage} 
            disabled={!input.trim() || isLoading}
            size="icon"
          >
            <Send className="h-4 w-4" />
          </Button>
        </div>
      </CardFooter>
    </Card>
  )
}
```

#### StreamingMessage

AIレスポンスのストリーミング表示を行うコンポーネントです。

```tsx
"use client"

import { useEffect, useState } from "react"
import { Bot } from "lucide-react"
import { Avatar } from "@/components/ui/avatar"

interface StreamingMessageProps {
  content: string
  isStreaming: boolean
  streamingDelay?: number
}

export function StreamingMessage({ 
  content, 
  isStreaming, 
  streamingDelay = 20 
}: StreamingMessageProps) {
  const [displayedContent, setDisplayedContent] = useState("")
  const [currentIndex, setCurrentIndex] = useState(0)

  // ストリーミングエフェクトを実装
  useEffect(() => {
    if (!isStreaming) {
      setDisplayedContent(content)
      return
    }

    if (currentIndex < content.length) {
      const timer = setTimeout(() => {
        setDisplayedContent(prev => prev + content[currentIndex])
        setCurrentIndex(prev => prev + 1)
      }, streamingDelay)
      
      return () => clearTimeout(timer)
    }
  }, [content, currentIndex, isStreaming, streamingDelay])

  // 新しいメッセージが来たらリセット
  useEffect(() => {
    setDisplayedContent("")
    setCurrentIndex(0)
  }, [content])

  return (
    <div className="flex gap-3 max-w-[80%]">
      <Avatar className="h-8 w-8 bg-primary-100">
        <Bot className="h-5 w-5 text-primary-700" />
      </Avatar>
      <div className="rounded-lg p-3 bg-primary-50 text-neutral-800">
        {displayedContent}
        {isStreaming && currentIndex < content.length && (
          <span className="inline-block w-1 h-4 ml-0.5 bg-primary-500 animate-pulse" />
        )}
      </div>
    </div>
  )
}
```

#### AIFeedbackRating

AIレスポンスに対するフィードバック評価コンポーネントです。

```tsx
import { useState } from "react"
import { Button } from "@/components/ui/button"
import { ThumbsUp, ThumbsDown } from "lucide-react"
import { Textarea } from "@/components/ui/textarea"

interface AIFeedbackRatingProps {
  messageId: string
  onFeedback: (messageId: string, isHelpful: boolean, comment?: string) => void
}

export function AIFeedbackRating({ messageId, onFeedback }: AIFeedbackRatingProps) {
  const [feedback, setFeedback] = useState<"helpful" | "unhelpful" | null>(null)
  const [comment, setComment] = useState("")
  const [isSubmitted, setIsSubmitted] = useState(false)

  const handleFeedback = (isHelpful: boolean) => {
    setFeedback(isHelpful ? "helpful" : "unhelpful")
  }

  const handleSubmit = () => {
    onFeedback(messageId, feedback === "helpful", comment)
    setIsSubmitted(true)
  }

  if (isSubmitted) {
    return (
      <div className="text-xs text-neutral-500 mt-2">
        フィードバックをありがとうございました
      </div>
    )
  }

  return (
    <div className="mt-2 space-y-2">
      <div className="flex items-center space-x-2">
        <span className="text-xs text-neutral-500">この回答は役に立ちましたか？</span>
        <Button 
          variant={feedback === "helpful" ? "default" : "outline"} 
          size="sm" 
          className="h-7 px-2"
          onClick={() => handleFeedback(true)}
        >
          <ThumbsUp className="h-3.5 w-3.5 mr-1" />
          はい
        </Button>
        <Button 
          variant={feedback === "unhelpful" ? "default" : "outline"} 
          size="sm" 
          className="h-7 px-2"
          onClick={() => handleFeedback(false)}
        >
          <ThumbsDown className="h-3.5 w-3.5 mr-1" />
          いいえ
        </Button>
      </div>
      
      {feedback === "unhelpful" && (
        <div className="space-y-2">
          <Textarea
            placeholder="改善のためにフィードバックをお願いします"
            className="h-20"
            value={comment}
            onChange={(e) => setComment(e.target.value)}
          />
          <Button size="sm" onClick={handleSubmit}>
            送信
          </Button>
        </div>
      )}
      
      {feedback === "helpful" && (
        <Button size="sm" onClick={handleSubmit}>
          送信
        </Button>
      )}
    </div>
  )
}
```

## 4. レイアウトシステム

### 4.1 グリッドシステム

HRX-AIでは、Tailwind CSSのフレックスボックスとグリッドシステムを活用して、適応性の高いレイアウトを構築します。

```tsx
// 基本グリッド（12カラムシステム）
<div className="grid grid-cols-12 gap-4">
  <div className="col-span-12 md:col-span-6 lg:col-span-3">
    {/* コンテンツ */}
  </div>
  <div className="col-span-12 md:col-span-6 lg:col-span-9">
    {/* コンテンツ */}
  </div>
</div>

// フレックスボックスを使用した水平レイアウト
<div className="flex flex-col md:flex-row gap-4">
  <div className="w-full md:w-1/3">
    {/* サイドバー */}
  </div>
  <div className="w-full md:w-2/3">
    {/* メインコンテンツ */}
  </div>
</div>
```

### 4.2 ページテンプレート

一貫したユーザー体験を提供するための標準ページテンプレートです。

```tsx
// components/layouts/dashboard-layout.tsx
import { MainNav } from "@/components/main-nav"
import { UserNav } from "@/components/user-nav"
import { Breadcrumb } from "@/components/ui/breadcrumb"
import { ReactNode } from "react"

interface DashboardLayoutProps {
  children: ReactNode
  pageTitle: string
  breadcrumbs?: Array<{
    title: string
    href?: string
  }>
}

export function DashboardLayout({ 
  children, 
  pageTitle,
  breadcrumbs = []
}: DashboardLayoutProps) {
  return (
    <div className="flex min-h-screen flex-col">
      <header className="sticky top-0 z-40 border-b bg-background">
        <div className="container flex h-16 items-center">
          <MainNav />
          <div className="ml-auto flex items-center space-x-4">
            <UserNav />
          </div>
        </div>
      </header>
      <div className="container flex-1 space-y-4 py-4">
        {breadcrumbs.length > 0 && (
          <Breadcrumb items={breadcrumbs} />
        )}
        <div className="flex items-center justify-between">
          <h1 className="text-2xl font-bold tracking-tight">{pageTitle}</h1>
        </div>
        <div className="flex-1">{children}</div>
      </div>
      <footer className="border-t py-4">
        <div className="container flex flex-col items-center justify-between gap-4 md:h-14 md:flex-row">
          <p className="text-center text-sm text-muted-foreground">
            &copy; {new Date().getFullYear()} HRX-AI. All rights reserved.
          </p>
        </div>
      </footer>
    </div>
  )
}
```

### 4.3 レスポンシブデザイン

HRX-AIのUIは、全てのデバイスで最適な体験を提供するためのレスポンシブデザインを採用しています。

**ブレークポイント:**

```typescript
// tailwind.config.js
const screens = {
  xs: '475px',
  sm: '640px',
  md: '768px',
  lg: '1024px',
  xl: '1280px',
  '2xl': '1536px',
}
```

**レスポンシブパターン:**

```tsx
// モバイルファースト + プログレッシブエンハンスメント
<div className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
  {/* レスポンシブカード */}
</div>

// コンポーネントの適応的表示
<div className="hidden md:block">
  {/* デスクトップのみ表示するコンポーネント */}
</div>
<div className="block md:hidden">
  {/* モバイルのみ表示するコンポーネント */}
</div>

// テキストサイズの調整
<h1 className="text-2xl md:text-3xl lg:text-4xl">
  レスポンシブ見出し
</h1>

// ナビゲーションのレスポンシブ切り替え
<nav className="hidden md:flex gap-4">
  {/* デスクトップナビゲーション */}
</nav>
<Button className="md:hidden" size="icon">
  {/* モバイルメニューボタン */}
</Button>
```

## 5. 状態と変異

UIコンポーネントの状態とその視覚的表現は、一貫した原則に基づいて設計します。

### 主要な状態

- **Default**: 通常状態
- **Hover**: カーソルが要素の上にある状態
- **Focus**: キーボードフォーカスを受けている状態
- **Active/Pressed**: クリックやタップされている状態
- **Disabled**: 操作できない状態
- **Loading**: 処理中の状態
- **Error**: エラーや無効な入力がある状態
- **Success**: 正常に完了した状態

### 状態表現例

```tsx
// ボタンの状態
<Button>デフォルト</Button>
<Button className="hover:bg-primary-600">ホバー（デモ用）</Button>
<Button className="focus:ring-2">フォーカス（デモ用）</Button>
<Button className="active:bg-primary-700">アクティブ（デモ用）</Button>
<Button disabled>無効</Button>
<Button isLoading>読み込み中</Button>

// フォーム要素の状態
<Input placeholder="デフォルト" />
<Input 
  placeholder="エラー" 
  className="border-danger-500 focus:ring-danger-500" 
  aria-invalid="true"
/>
<Input 
  placeholder="成功" 
  className="border-success-500 focus:ring-success-500" 
/>
<Input disabled placeholder="無効" />
```

## 6. アニメーションとトランジション

ユーザー体験を向上させ、フィードバックを提供するためのアニメーション原則です。

### アニメーション変数

```css
/* globals.css */
:root {
  --animation-duration-fast: 150ms;
  --animation-duration-normal: 250ms;
  --animation-duration-slow: 350ms;
  --animation-easing-default: cubic-bezier(0.4, 0, 0.2, 1);
  --animation-easing-in: cubic-bezier(0.4, 0, 1, 1);
  --animation-easing-out: cubic-bezier(0, 0, 0.2, 1);
}
```

### 標準トランジション

```tsx
// tailwind.config.js
const transitionProperty = {
  'none': 'none',
  'all': 'all',
  'default': 'color, background-color, border-color, text-decoration-color, fill, stroke, opacity, box-shadow, transform, filter, backdrop-filter',
  'colors': 'color, background-color, border-color, text-decoration-color, fill, stroke',
  'opacity': 'opacity',
  'shadow': 'box-shadow',
  'transform': 'transform',
}

const transitionTimingFunction = {
  'default': 'cubic-bezier(0.4, 0, 0.2, 1)',
  'linear': 'linear',
  'in': 'cubic-bezier(0.4, 0, 1, 1)',
  'out': 'cubic-bezier(0, 0, 0.2, 1)',
  'in-out': 'cubic-bezier(0.4, 0, 0.2, 1)',
}

const transitionDuration = {
  'fast': '150ms',
  'normal': '250ms',
  'slow': '350ms',
  '75': '75ms',
  '100': '100ms',
  '150': '150ms',
  '200': '200ms',
  '250': '250ms',
  '300': '300ms',
  '350': '350ms',
  '500': '500ms',
  '700': '700ms',
  '1000': '1000ms',
}
```

### アニメーション例

```tsx
// ボタンのホバーアニメーション
<Button className="transition-all duration-normal hover:scale-105">
  スケールアニメーション
</Button>

// フェードインアニメーション
<div className="animate-fade-in">
  フェードイン要素
</div>

// データローディングプレースホルダ
<div className="h-8 w-full bg-neutral-200 animate-pulse rounded">
  {/* ローディングプレースホルダ */}
</div>

// AI返答のタイピングアニメーション
<StreamingMessage 
  content="AIからの応答メッセージです"
  isStreaming={true}
  streamingDelay={30}
/>
```

## 7. アクセシビリティガイドライン

HRX-AIは、WCAG 2.1 AA準拠を目指し、以下のアクセシビリティ原則に従っています。

### 基本原則

1. **知覚可能性**: 全てのユーザーがコンテンツを認識できるようにする
2. **操作可能性**: UIコンポーネントとナビゲーションを操作可能にする
3. **理解可能性**: 情報と操作を理解可能にする
4. **堅牢性**: 様々な支援技術に対応するコンテンツを提供する

### 実装ガイドライン

```tsx
// 適切なセマンティックHTML
<main>
  <h1>メインタイトル</h1>
  <section aria-labelledby="section-title">
    <h2 id="section-title">セクションタイトル</h2>
    <p>セクションコンテンツ</p>
  </section>
</main>

// キーボード操作のサポート
<Button onKeyDown={(e) => {
  if (e.key === 'Enter' || e.key === ' ') {
    // アクション実行
  }
}}>
  アクション
</Button>

// スクリーンリーダーのサポート
<Button aria-label="設定を開く">
  <Settings className="h-4 w-4" />
</Button>

// フォーカス表示
<Input className="focus:ring-2 focus:ring-primary-500" />

// ライブリージョン（動的更新時）
<div aria-live="polite" aria-atomic="true">
  {notification}
</div>

// 色のコントラスト
<p className="text-neutral-800 bg-white">
  高コントラストテキスト
</p>

// スクリーンリーダー専用テキスト
<span className="sr-only">スクリーンリーダーにのみ読み上げられます</span>
```

## 8. 実装ガイド

### コンポーネント使用のベストプラクティス

1. **一貫性を保つ**:
   - 同じ用途には同じコンポーネントを使用
   - プロパティとバリアントを一貫して適用

2. **コンポーネントの構成**:
   - 小さなコンポーネントを組み合わせて複雑なUIを構築
   - コンポーネントの役割と責任を明確に分離

3. **命名規則**:
   - カスタムコンポーネント名は意味のある名前をPascalCase（例: `EmployeeCard`）
   - インスタンス変数はcamelCase（例: `const primaryButton = ...`）

4. **プロパティの使用**:
   - 必須プロパティには型を明示
   - オプショナルプロパティには適切なデフォルト値を設定
   - 不要なプロパティの伝播を避ける

```tsx
// 良い例: コンポーネント構成
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card"
import { Avatar } from "@/components/ui/avatar"
import { Badge } from "@/components/ui/badge"

interface EmployeeCardProps {
  name: string;
  position: string;
  department: string;
  imageUrl?: string;
  status?: 'active' | 'on-leave' | 'terminated';
}

export function EmployeeCard({
  name,
  position,
  department,
  imageUrl,
  status = 'active'
}: EmployeeCardProps) {
  const getStatusBadge = () => {
    switch (status) {
      case 'active':
        return <Badge variant="outline" className="bg-success-50 text-success-600 border-success-200">在職中</Badge>;
      case 'on-leave':
        return <Badge variant="outline" className="bg-warning-50 text-warning-600 border-warning-200">休職中</Badge>;
      case 'terminated':
        return <Badge variant="outline" className="bg-neutral-50 text-neutral-600 border-neutral-200">退職</Badge>;
      default:
        return null;
    }
  };
  
  return (
    <Card>
      <CardHeader className="flex flex-row items-center gap-4">
        <Avatar>
          <AvatarImage src={imageUrl} alt={name} />
          <AvatarFallback>{name.charAt(0)}</AvatarFallback>
        </Avatar>
        <div className="flex flex-col">
          <CardTitle className="text-lg">{name}</CardTitle>
          <p className="text-sm text-neutral-500">{position}</p>
        </div>
        <div className="ml-auto">
          {getStatusBadge()}
        </div>
      </CardHeader>
      <CardContent>
        <div className="text-sm">
          <span className="font-medium">部署: </span>
          {department}
        </div>
      </CardContent>
    </Card>
  )
}
```

### テーマのカスタマイズ

```tsx
// tailwind.config.js
module.exports = {
  darkMode: ["class"],
  content: [
    './pages/**/*.{ts,tsx}',
    './components/**/*.{ts,tsx}',
    './app/**/*.{ts,tsx}',
    './src/**/*.{ts,tsx}',
  ],
  prefix: "",
  theme: {
    container: {
      center: true,
      padding: "2rem",
      screens: {
        "2xl": "1400px",
      },
    },
    extend: {
      colors: {
        // カラーパレットの拡張
      },
      fontFamily: {
        // フォントの追加
      },
      borderRadius: {
        lg: "var(--radius)",
        md: "calc(var(--radius) - 2px)",
        sm: "calc(var(--radius) - 4px)",
      },
      keyframes: {
        "fade-in": {
          "0%": { opacity: 0 },
          "100%": { opacity: 1 },
        },
        "fade-out": {
          "0%": { opacity: 1 },
          "100%": { opacity: 0 },
        },
        // その他のキーフレーム
      },
      animation: {
        "fade-in": "fade-in 0.3s ease-out",
        "fade-out": "fade-out 0.3s ease-in",
        // その他のアニメーション
      },
    },
  },
  plugins: [require("tailwindcss-animate")],
}
```

## 9. 使用例とパターン

### ダッシュボードパターン

```tsx
import { DashboardLayout } from "@/components/layouts/dashboard-layout"
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card"
import { Tabs, TabsList, TabsTrigger, TabsContent } from "@/components/ui/tabs"
import { Chart } from "@/components/ui/chart"

export default function Dashboard() {
  return (
    <DashboardLayout 
      pageTitle="ダッシュボード" 
      breadcrumbs={[
        { title: "ホーム", href: "/" },
        { title: "ダッシュボード" }
      ]}
    >
      <div className="space-y-4">
        <div className="grid gap-4 md:grid-cols-2 lg:grid-cols-4">
          <Card>
            <CardHeader className="flex flex-row items-center justify-between space-y-0 pb-2">
              <CardTitle className="text-sm font-medium">
                総従業員数
              </CardTitle>
            </CardHeader>
            <CardContent>
              <div className="text-2xl font-bold">1,234</div>
              <p className="text-xs text-neutral-500">
                前月比 +2.5%
              </p>
            </CardContent>
          </Card>
          <Card>
            <CardHeader className="flex flex-row items-center justify-between space-y-0 pb-2">
              <CardTitle className="text-sm font-medium">
                離職率
              </CardTitle>
            </CardHeader>
            <CardContent>
              <div className="text-2xl font-bold">3.2%</div>
              <p className="text-xs text-neutral-500">
                前月比 -0.5%
              </p>
            </CardContent>
          </Card>
          <Card>
            <CardHeader className="flex flex-row items-center justify-between space-y-0 pb-2">
              <CardTitle className="text-sm font-medium">
                平均在籍期間
              </CardTitle>
            </CardHeader>
            <CardContent>
              <div className="text-2xl font-bold">2.7年</div>
              <p className="text-xs text-neutral-500">
                前年比 +0.3年
              </p>
            </CardContent>
          </Card>
          <Card>
            <CardHeader className="flex flex-row items-center justify-between space-y-0 pb-2">
              <CardTitle className="text-sm font-medium">
                エンゲージメントスコア
              </CardTitle>
            </CardHeader>
            <CardContent>
              <div className="text-2xl font-bold">7.8/10</div>
              <p className="text-xs text-neutral-500">
                前回比 +0.2
              </p>
            </CardContent>
          </Card>
        </div>
        
        <Tabs defaultValue="overview" className="space-y-4">
          <TabsList>
            <TabsTrigger value="overview">概要</TabsTrigger>
            <TabsTrigger value="analytics">分析</TabsTrigger>
            <TabsTrigger value="reports">レポート</TabsTrigger>
          </TabsList>
          <TabsContent value="overview" className="space-y-4">
            <div className="grid gap-4 md:grid-cols-2 lg:grid-cols-7">
              <Card className="col-span-4">
                <CardHeader>
                  <CardTitle>部門別従業員数</CardTitle>
                </CardHeader>
                <CardContent className="pl-2">
                  <Chart 
                    type="bar"
                    height={300}
                    data={[
                      { id: 'エンジニアリング', value: 420 },
                      { id: '営業', value: 340 },
                      { id: 'マーケティング', value: 270 },
                      { id: '人事', value: 90 },
                      { id: '財務', value: 60 },
                      { id: '経営', value: 54 }
                    ]}
                  />
                </CardContent>
              </Card>
              <Card className="col-span-3">
                <CardHeader>
                  <CardTitle>離職理由</CardTitle>
                </CardHeader>
                <CardContent>
                  <Chart 
                    type="pie"
                    height={300}
                    data={[
                      { id: 'キャリア成長', value: 35 },
                      { id: '報酬', value: 25 },
                      { id: 'ワークライフバランス', value: 20 },
                      { id: '企業文化', value: 15 },
                      { id: 'その他', value: 5 }
                    ]}
                  />
                </CardContent>
              </Card>
            </div>
          </TabsContent>
        </Tabs>
      </div>
    </DashboardLayout>
  )
}
```

### フォームパターン

```tsx
import { useState } from "react"
import { Button } from "@/components/ui/button"
import { Card, CardContent, CardFooter, CardHeader, CardTitle } from "@/components/ui/card"
import { Input } from "@/components/ui/input"
import { Label } from "@/components/ui/label"
import { Select, SelectContent, SelectItem, SelectTrigger, SelectValue } from "@/components/ui/select"
import { Separator } from "@/components/ui/separator"
import { useToast } from "@/components/ui/use-toast"

export function EmployeeForm() {
  const { toast } = useToast()
  const [isLoading, setIsLoading] = useState(false)
  
  const handleSubmit = async (e) => {
    e.preventDefault()
    setIsLoading(true)
    
    // 保存処理のモック
    await new Promise(resolve => setTimeout(resolve, 1000))
    
    setIsLoading(false)
    toast({
      title: "従業員情報が保存されました",
      description: "従業員レコードが正常に更新されました。",
    })
  }
  
  return (
    <Card>
      <CardHeader>
        <CardTitle>従業員情報編集</CardTitle>
      </CardHeader>
      <form onSubmit={handleSubmit}>
        <CardContent className="space-y-6">
          <div className="space-y-4">
            <h3 className="text-lg font-medium">基本情報</h3>
            
            <div className="grid grid-cols-1 gap-4 md:grid-cols-2">
              <div className="space-y-2">
                <Label htmlFor="firstName">名</Label>
                <Input id="firstName" placeholder="太郎" defaultValue="太郎" />
              </div>
              <div className="space-y-2">
                <Label htmlFor="lastName">姓</Label>
                <Input id="lastName" placeholder="山田" defaultValue="山田" />
              </div>
            </div>
            
            <div className="grid grid-cols-1 gap-4 md:grid-cols-2">
              <div className="space-y-2">
                <Label htmlFor="email">メールアドレス</Label>
                <Input id="email" type="email" placeholder="taro.yamada@example.com" defaultValue="taro.yamada@example.com" />
              </div>
              <div className="space-y-2">
                <Label htmlFor="phone">電話番号</Label>
                <Input id="phone" placeholder="090-1234-5678" defaultValue="090-1234-5678" />
              </div>
            </div>
          </div>
          
          <Separator />
          
          <div className="space-y-4">
            <h3 className="text-lg font-medium">勤務情報</h3>
            
            <div className="grid grid-cols-1 gap-4 md:grid-cols-2">
              <div className="space-y-2">
                <Label htmlFor="department">部署</Label>
                <Select defaultValue="sales">
                  <SelectTrigger id="department">
                    <SelectValue placeholder="部署を選択" />
                  </SelectTrigger>
                  <SelectContent>
                    <SelectItem value="engineering">エンジニアリング</SelectItem>
                    <SelectItem value="sales">営業</SelectItem>
                    <SelectItem value="marketing">マーケティング</SelectItem>
                    <SelectItem value="hr">人事</SelectItem>
                    <SelectItem value="finance">財務</SelectItem>
                  </SelectContent>
                </Select>
              </div>
              <div className="space-y-2">
                <Label htmlFor="position">役職</Label>
                <Input id="position" placeholder="マネージャー" defaultValue="マネージャー" />
              </div>
            </div>
            
            <div className="grid grid-cols-1 gap-4 md:grid-cols-2">
              <div className="space-y-2">
                <Label htmlFor="hireDate">入社日</Label>
                <Input id="hireDate" type="date" defaultValue="2022-04-01" />
              </div>
              <div className="space-y-2">
                <Label htmlFor="employmentType">雇用形態</Label>
                <Select defaultValue="full-time">
                  <SelectTrigger id="employmentType">
                    <SelectValue placeholder="雇用形態を選択" />
                  </SelectTrigger>
                  <SelectContent>
                    <SelectItem value="full-time">正社員</SelectItem>
                    <SelectItem value="part-time">パートタイム</SelectItem>
                    <SelectItem value="contract">契約社員</SelectItem>
                    <SelectItem value="temporary">派遣社員</SelectItem>
                  </SelectContent>
                </Select>
              </div>
            </div>
          </div>
        </CardContent>
        <CardFooter className="flex justify-between">
          <Button variant="outline" type="button">キャンセル</Button>
          <Button type="submit" disabled={isLoading}>
            {isLoading ? "保存中..." : "保存"}
          </Button>
        </CardFooter>
      </form>
    </Card>
  )
}
```

## 10. カスタマイズと拡張

### テーマのカスタマイズ

```tsx
// lib/themes.ts
export const lightTheme = {
  // デフォルトライトテーマ
}

export const darkTheme = {
  // ダークテーマ
}

export const brandedTheme = {
  // 特定企業向けカスタマイズテーマ（例：企業カラーに合わせた配色）
}

// 使用例
import { ThemeProvider } from "@/components/theme-provider"
import { brandedTheme } from "@/lib/themes"

<ThemeProvider theme={brandedTheme}>
  <App />
</ThemeProvider>
```

### カスタムコンポーネントの作成

```tsx
// components/custom/branded-card.tsx
import { Card, CardProps } from "@/components/ui/card"
import { cn } from "@/lib/utils"

interface BrandedCardProps extends CardProps {
  priority?: 'low' | 'medium' | 'high'
}

export function BrandedCard({ priority, className, ...props }: BrandedCardProps) {
  return (
    <Card
      className={cn(
        className,
        priority === 'high' && "border-l-4 border-l-danger-500",
        priority === 'medium' && "border-l-4 border-l-warning-500",
        priority === 'low' && "border-l-4 border-l-success-500",
      )}
      {...props}
    />
  )
}
```

### サードパーティライブラリの統合

```tsx
// components/ui/date-range-picker.tsx
// react-datepickerを使用したカスタムコンポーネント例
import DatePicker from "react-datepicker"
import "react-datepicker/dist/react-datepicker.css"
import { Button } from "@/components/ui/button"
import { CalendarIcon } from "lucide-react"
import { useState } from "react"
import { cn } from "@/lib/utils"

interface DateRangePickerProps {
  startDate: Date | null
  endDate: Date | null
  onChange: (dates: [Date | null, Date | null]) => void
  className?: string
}

export function DateRangePicker({
  startDate,
  endDate,
  onChange,
  className
}: DateRangePickerProps) {
  const [isOpen, setIsOpen] = useState(false)

  return (
    <div className={cn("relative", className)}>
      <Button
        variant="outline"
        className={cn(
          "w-full justify-start text-left font-normal",
          !startDate && !endDate && "text-muted-foreground"
        )}
        onClick={() => setIsOpen(!isOpen)}
      >
        <CalendarIcon className="mr-2 h-4 w-4" />
        {startDate && endDate ? (
          <>
            {startDate.toLocaleDateString()} - {endDate.toLocaleDateString()}
          </>
        ) : (
          <span>日付範囲を選択</span>
        )}
      </Button>
      <div className={cn("absolute z-50", !isOpen && "hidden")}>
        <DatePicker
          selected={startDate}
          onChange={onChange}
          startDate={startDate}
          endDate={endDate}
          selectsRange
          inline
        />
      </div>
    </div>
  )
}
```

---


