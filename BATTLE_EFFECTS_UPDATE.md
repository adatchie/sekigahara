# 戦闘エフェクト改善計画

## ユーザーフィードバック

1. **火花エフェクト** - 現状は花火のように広く散っている → 攻撃側と防御側の間で小さく閃く程度に変更
2. **火花のサイズ** - 大きな玉型 → 数ドットの小さな光に変更  
3. **効果音** - シンセ音ではイメージと違う → 外部サウンドファイルを使用して雰囲気のあるSEに変更

## 必要な修正

### 1. combat.js - spawnSparks関数
```javascript
// 現在: 防御側の位置に放射状に散らばる
spawnSparks(pos, count) { ... }

// ⇒ 修正後: 攻撃側と防御側の中間点に小さく集中
spawnSparks(attPos, defPos) {
    const midX = (attPos.x + defPos.x) / 2;
    const midY = (attPos.y + defPos.y) / 2;
    // 中間点付近の狭い範囲（±15px）に15個の火花
    // 動きも小さく（速度±0.8）
}
```

### 2. combat.js - combat関数での呼び出し
```javascript
// 現在
this.spawnSparks(def.pos, 8);

// ⇒ 修正後
this.spawnSparks(att.pos, def.pos);
```

### 3. rendering.js - fire花の描画
```javascript
// 現在: 5-10pxの大きなグラデーション球
// ⇒ 修正後: 1.5-2.5pxの小さな光点

ctx.fillStyle = `rgba(255, 255, 150, ${alpha})`;
ctx.arc(sx, sy, size, 0, Math.PI * 2); // size = 1.5～2.5px
```

### 4. audio.js - 外部SE対応
プログラム的に音を生成するのではなく、HTMLAudioElement で外部ファイルを再生する方式に変更。

`sounds/` フォルダに以下のファイルを配置:
- `battle_cry.mp3` - 鬨の声
- `victory_slash.mp3` - 勝利時の斬撃音
- `defeat_slash.mp3` - 敗北時の斬撃音

## 次のステップ

ユーザーが適切なSEファイルを用意するか、フリー素材を使用することを推奨します。
