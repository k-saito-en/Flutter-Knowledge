# adaptive_dialog

### 概要

- プラットフォームに応じてアラートダイアログまたはモーダルアクションシートを適応的に表示します。
- 機能
  - [showOkAlertDialog](https://pub.dev/documentation/adaptive_dialog/latest/adaptive_dialog/showOkAlertDialog.html)
  - [showOkCancelAlertDialog](https://pub.dev/documentation/adaptive_dialog/latest/adaptive_dialog/showOkCancelAlertDialog.html)
  - [showConfirmationDialog](https://pub.dev/documentation/adaptive_dialog/latest/adaptive_dialog/showConfirmationDialog.html)
    - 選ばせるやつ
  - etc...

### 引用記事

- [adaptive_dialog 1.8.0+1 ](https://pub.dev/packages/adaptive_dialog) 2022/9

### 使い方
- `showOkAlertDialog`
  ```dart
  Future<OkCancelResult> showOkAlertDialog({
    required BuildContext context,
    String? title,
    String? message,
    String? okLabel,
    bool barrierDismissible = true,
    @Deprecated('Use `style` instead.') AdaptiveStyle? alertStyle,
    AdaptiveStyle? style,
    @Deprecated('Use `ios` instead. Will be removed in v2.')
        bool useActionSheetForCupertino = false,
    bool useActionSheetForIOS = false,
    bool useRootNavigator = true,
    VerticalDirection actionsOverflowDirection = VerticalDirection.up,
    bool fullyCapitalizedForMaterial = true,
    WillPopCallback? onWillPop,
    AdaptiveDialogBuilder? builder,
    RouteSettings? routeSettings,
  }) async {
    final theme = Theme.of(context);
    final adaptiveStyle = style ?? AdaptiveDialog.instance.defaultStyle;
    final isMacOS = adaptiveStyle.effectiveStyle(theme) == AdaptiveStyle.macOS;
    final result = await showAlertDialog<OkCancelResult>(
      routeSettings: routeSettings,
      context: context,
      title: title,
      message: message,
      barrierDismissible: barrierDismissible,
      style: alertStyle ?? style,
      useActionSheetForIOS: useActionSheetForCupertino || useActionSheetForIOS,
      useRootNavigator: useRootNavigator,
      actionsOverflowDirection: actionsOverflowDirection,
      fullyCapitalizedForMaterial: fullyCapitalizedForMaterial,
      onWillPop: onWillPop,
      builder: builder,
      actions: [
        AlertDialogAction(
          label: okLabel ?? MaterialLocalizations.of(context).okButtonLabel,
          key: OkCancelResult.ok,
          isDefaultAction: isMacOS,
        )
      ],
    );
    return result ?? OkCancelResult.cancel;
  }
  ```

