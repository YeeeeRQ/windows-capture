# windows-capture (Fork)

基于 [NiiightmareXD/windows-capture](https://github.com/NiiightmareXD/windows-capture) v1.5.0 的修改版本。

## 修改变更

### 主要修改：Graceful Fallback 机制

当 Graphics Capture API 某些功能在当前系统上不支持时，不再抛出错误，改为输出警告并继续执行。

**解决的问题**：
- 错误信息：`"Toggling the capture border is not supported by the Graphics Capture API on this platform"`
- 同样问题影响：`CursorCaptureSettings`、`DrawBorderSettings`、`SecondaryWindowSettings`、`MinimumUpdateIntervalSettings`、`DirtyRegionSettings`

**受影响的 API**：
- `is_border_settings_supported()`
- `is_cursor_settings_supported()`
- `is_secondary_windows_supported()`
- `is_minimum_update_interval_supported()`
- `is_dirty_region_supported()`

### 根本原因

`is_*_settings_supported()` 系列函数通过 WinRT 元数据检测属性是否存在：

```rust
ApiInformation::IsPropertyPresent(
    &HSTRING::from("Windows.Graphics.Capture.GraphicsCaptureSession"),
    &HSTRING::from("IsBorderRequired"),
)?
```

在某些 Windows 系统上（如 Windows 10 21H2），虽然 Graphics Capture API 本身工作正常，但 WinRT 元数据可能错误地报告 `IsBorderRequired` 属性不存在，导致函数返回 `false`。

**实际测试结果**：检测返回 `false` 时，截图和录屏功能仍然正常工作，只是边框等附加功能不会生效。

## 使用方法

```toml
[dependencies]
windows-capture = { git = "https://github.com/YeeeeRQ/windows-capture" }
```

## 适用场景

- Windows 10/11 系统
- 遇到 "Toggling the capture border is not supported" 或类似错误
- 需要更稳定的基本屏幕捕获功能

## 与原版差异

| 特性 | 原版 (NiiightmareXD) | Fork (YeeeeRQ) |
|------|------------------------|----------------|
| 版本 | 2.0.0-alpha.7 / 1.5.0 | 1.5.0 |
| 不支持的功能 | 返回错误，程序退出 | 输出警告，继续执行 |
| 截图功能 | 可能报错退出 | 正常 |
| 录屏功能 | 可能报错退出 | 正常 |

## 原仓库

- Fork 自：https://github.com/NiiightmareXD/windows-capture
- 本 Fork：https://github.com/YeeeeRQ/windows-capture
