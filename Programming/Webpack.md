# Настройка Webpack на примере проекта по разработке темы для Wordpress

## Инициализация репозитория
Создаст после нескольких вопросов файл package.json в текущей папке
`yarn init`

## Установка Webpack
Команда создаст папку node_modules, в которую установит Webpack с зависимостями, и добавит Webpack в devDependencies файла package.json (благодаря флагу --dev)
`yarn add --dev webpack`

## Установка Babel
Установит модули:
- @babel/core (библиотека для преобразования современного JavaScript в обратно совместимую версию)
- @babel/preset-env (набор пресетов для различных окружений)
- babel-loader (библиотека, которая позволяет использовать Babel из Webpack)
`yarn add --dev babel-loader @babel/core @babel/preset-env`

## Пишем скрипт для сборки
В файл _package.json_:
```json
{
    ...,
    "scripts": {
        "dev": "webpack --mode=development"
    },
    ...
}
```

## Проверяем
В файле _src/index.js_ пишем:
`var [a,,b] = [1, 2, 3]`
В консоли запускаем команду:
`yarn run dev`
На выходе получаем в файле _dist/bundle.js_:
```js
/* ... */
eval("var _ref = [1, 2, 3],\n    a = _ref[0],\n    b = _ref[2];\n\n//# sourceURL=webpack:///./src/index.js?");
```

## Установка модулей для SASS
`yarn add --dev css-loader postcss-loader sass-loader mini-css-extract-plugin node-sass postcss-import postcss-preset-env cssnano`

## Определяем загрузчики
В файл _webpack.config.js_ добавляем:
```js
module.exports = {
  /* ... */
  module: {
    rules: [
      /* ... */
      {
        test: /\.s?css$/,
        use: [
          {
            loader: MiniCssExtractPlugin.loader
          },
          {
            loader: 'css-loader',
            options: {
              sourceMap: true,
            },
          },
          {
            loader: 'postcss-loader',
            options: {
              sourceMap: true,
            },
          },
          {
            loader: 'sass-loader',
            options: {
              sourceMap: true,
            },
          },
        ]
      }
    ]
  },
  plugins: [
    new MiniCssExtractPlugin({
      filename: '[name].css',
      chunkFilename: '[id].css',
      ignoreOrder: false,
    }),
  ]
}
```

## Установка модулей для assets
`yarn add --dev file-loader`

## Конфигурация для картинок
```js
{
  test: /\.(png|jpg|gif)$/,
  use: [
    {
      loader: 'file-loader',
      options: {
        outputPath: 'assets/images',
        name: '[name].[ext]',
      },
    },
  ],
}
```
Картинки попадают в билд только если их явно импортировать в js-файлах