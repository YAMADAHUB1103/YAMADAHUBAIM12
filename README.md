# YAMADAHUBAIM12-- Services
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local CoreGui = game:GetService("CoreGui")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local SoundService = game:GetService("SoundService")

local localPlayer = Players.LocalPlayer
local camera = workspace.CurrentCamera

-- 既存のUIがあれば削除（重複防止）
if CoreGui:FindFirstChild("YadaHubMobile") then
    CoreGui.YadaHubMobile:Destroy()
end

-- メインGUIの作成
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "YadaHubMobile"
screenGui.ResetOnSpawn = false
screenGui.Parent = CoreGui

-- ==========================================
-- 起動時のBGM（音楽）再生パーツ
-- ==========================================
local bgm = Instance.new("Sound")
bgm.Name = "YadaHubBGM"
bgm.SoundId = "rbxassetid://9043232530" -- Robloxの動作確認用ポピュラー音楽ID
bgm.Volume = 1.0 -- 音量 (0.0 〜 1.0)
bgm.Looped = true -- ループ再生
bgm.Parent = SoundService
bgm:Play()

-- ==========================================
-- 起動時の画面いっぱい「山田hub」ド迫力点滅演出
-- ==========================================
local introGui = Instance.new("Frame")
introGui.Size = UDim2.new(1, 0, 1, 0)
introGui.BackgroundColor3 = Color3.fromRGB(5, 5, 5)
introGui.BorderSizePixel = 0
introGui.ZIndex = 10
introGui.Parent = screenGui

local introText = Instance.new("TextLabel")
introText.Size = UDim2.new(1, 0, 1, 0)
introText.BackgroundTransparency = 1
introText.Text = "山田hub"
introText.TextColor3 = Color3.fromRGB(255, 100, 255)
introText.TextSize = 100
introText.Font = Enum.Font.SourceSansBold
introText.TextScaled = true
introText.ZIndex = 11
introText.Parent = introGui

-- 点滅ループ＆一定時間後に消去する処理
local introActive = true
task.spawn(function()
    local t = 0
    while introActive do
        t = t + 0.15
        introText.TextColor3 = Color3.fromHSV((t % 4) / 4, 1, 1)
        introText.TextTransparency = math.abs(math.sin(t * 5)) * 0.5
        task.wait(0.03)
    end
end)

-- 3秒後にイントロを自動で消す（フェードアウト）
task.delay(3, function()
    introActive = false
    local tweenInfo = TweenInfo.new(1, Enum.EasingStyle.Quad, Enum.EasingDirection.Out)
    local tween = TweenService:Create(introGui, tweenInfo, {BackgroundTransparency = 1})
    local tweenText = TweenService:Create(introText, tweenInfo, {TextTransparency = 1})
    tween:Play()
    tweenText:Play()
    tween.Completed:Connect(function()
        introGui:Destroy()
    end)
end)

-- ==========================================
-- メインUIの作成
-- ==========================================

-- 1. 「やまだhub」を開閉するためのアイコンボタン
local openButton = Instance.new("TextButton")
openButton.Size = UDim2.new(0, 55, 0, 55)
openButton.Position = UDim2.new(0, 20, 0, 20)
openButton.BackgroundColor3 = Color3.fromRGB(60, 20, 90)
openButton.TextColor3 = Color3.fromRGB(255, 255, 255)
openButton.Text = "山田"
openButton.TextSize = 15
openButton.Font = Enum.Font.SourceSansBold
openButton.Parent = screenGui

local openCorner = Instance.new("UICorner")
openCorner.CornerRadius = UDim.new(1, 0)
openCorner.Parent = openButton

local openStroke = Instance.new("UIStroke")
openStroke.Color = Color3.fromRGB(180, 100, 255)
openStroke.Thickness = 2
openStroke.Parent = openButton

-- 2. 設定画面のメインフレーム
local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 210, 0, 395)
mainFrame.Position = UDim2.new(0, 85, 0, 20)
mainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
mainFrame.Visible = false
mainFrame.Parent = screenGui

local frameCorner = Instance.new("UICorner")
frameCorner.CornerRadius = UDim.new(0, 8)
frameCorner.Parent = mainFrame

-- タイトル（メニュー内）
local menuTitle = Instance.new("TextLabel")
menuTitle.Size = UDim2.new(1, 0, 0, 30)
menuTitle.BackgroundTransparency = 1
menuTitle.Text = "YadaHub Custom"
menuTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
menuTitle.TextSize = 15
menuTitle.Font = Enum.Font.SourceSansBold
menuTitle.Parent = mainFrame

-- 設定状態変数
local aimbotEnabled = true
local xrayEnabled = false
local infinityJumpEnabled = true
local fovSize = 200
local teamCheck = true
local radiusVisualEnabled = true -- 足元円の表示状態

-- 3. Aimbot切り替えボタン
local aimbotButton = Instance.new("TextButton")
aimbotButton.Size = UDim2.new(0, 180, 0, 28)
aimbotButton.Position = UDim2.new(0, 15, 0, 35)
aimbotButton.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
aimbotButton.TextColor3 = Color3.fromRGB(255, 255, 255)
aimbotButton.Text = "Ativar Aimbot: ON"
aimbotButton.TextSize = 12
aimbotButton.Font = Enum.Font.SourceSans
aimbotButton.Parent = mainFrame
Instance.new("UICorner", aimbotButton).CornerRadius = UDim.new(0, 5)

aimbotButton.MouseButton1Click:Connect(function()
    aimbotEnabled = not aimbotEnabled
    aimbotButton.Text = aimbotEnabled and "Ativar Aimbot: ON" or "Ativar Aimbot: OFF"
end)

-- 4. インフィニティジャンプ切り替えボタン
local infJumpButton = Instance.new("TextButton")
infJumpButton.Size = UDim2.new(0, 180, 0, 28)
infJumpButton.Position = UDim2.new(0, 15, 0, 68)
infJumpButton.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
infJumpButton.TextColor3 = Color3.fromRGB(255, 255, 255)
infJumpButton.Text = "Infinite Jump: ON"
infJumpButton.TextSize = 12
infJumpButton.Font = Enum.Font.SourceSans
infJumpButton.Parent = mainFrame
Instance.new("UICorner", infJumpButton).CornerRadius = UDim.new(0, 5)

infJumpButton.MouseButton1Click:Connect(function()
    infinityJumpEnabled = not infinityJumpEnabled
    infJumpButton.Text = infinityJumpEnabled and "Infinite Jump: ON" or "Infinite Jump: OFF"
end)

-- 5. 建築物半透明化切り替えボタン
local xrayButton = Instance.new("TextButton")
xrayButton.Size = UDim2.new(0, 180, 0, 28)
xrayButton.Position = UDim2.new(0, 15, 0, 101)
xrayButton.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
xrayButton.TextColor3 = Color3.fromRGB(255, 255, 255)
xrayButton.Text = "X-Ray (Builds): OFF"
xrayButton.TextSize = 12
xrayButton.Font = Enum.Font.SourceSans
xrayButton.Parent = mainFrame
Instance.new("UICorner", xrayButton).CornerRadius = UDim.new(0, 5)

xrayButton.MouseButton1Click:Connect(function()
    xrayEnabled = not xrayEnabled
    xrayButton.Text = xrayEnabled and "X-Ray (Builds): ON" or "X-Ray (Builds): OFF"
    
    for _, obj in ipairs(workspace:GetDescendants()) do
        if obj:IsA("BasePart") and not obj.Parent:FindFirstChildOfClass("Humanoid") and not obj:IsDescendantOf(localPlayer.Character) then
            local isGround = (obj.Size.Y < 3 and obj.Position.Y < (localPlayer.Character and localPlayer.Character.Head.Position.Y - 5 or 0)) or obj.Name == "Terrain" or obj.Name:lower():find("floor") or obj.Name:lower():find("ground")
            if not isGround then
                obj.Transparency = xrayEnabled and 0.6 or 0
            end
        end
    end
end)

-- 6. FOV表示ラベル & 調整ボタン
local fovLabel = Instance.new("TextLabel")
fovLabel.Size = UDim2.new(0, 180, 0, 18)
fovLabel.Position = UDim2.new(0, 15, 0, 134)
fovLabel.BackgroundTransparency = 1
fovLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
fovLabel.Text = "FOV: " .. fovSize
fovLabel.TextSize = 12
fovLabel.Font = Enum.Font.SourceSansBold
fovLabel.Parent = mainFrame

local fovMinus = Instance.new("TextButton")
fovMinus.Size = UDim2.new(0, 85, 0, 24)
fovMinus.Position = UDim2.new(0, 15, 0, 155)
fovMinus.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
fovMinus.TextColor3 = Color3.fromRGB(255, 255, 255)
fovMinus.Text = "FOV -"
fovMinus.TextSize = 12
fovMinus.Parent = mainFrame
Instance.new("UICorner", fovMinus).CornerRadius = UDim.new(0, 5)

local fovPlus = Instance.new("TextButton")
fovPlus.Size = UDim2.new(0, 85, 0, 24)
fovPlus.Position = UDim2.new(0, 110, 0, 155)
fovPlus.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
fovPlus.TextColor3 = Color3.fromRGB(255, 255, 255)
fovPlus.Text = "FOV +"
fovPlus.TextSize = 12
fovPlus.Parent = mainFrame
Instance.new("UICorner", fovPlus).CornerRadius = UDim.new(0, 5)

fovMinus.MouseButton1Click:Connect(function()
    fovSize = math.clamp(fovSize - 20, 50, 600)
    fovLabel.Text = "FOV: " .. fovSize
end)

fovPlus.MouseButton1Click:Connect(function()
    fovSize = math.clamp(fovSize + 20, 50, 600)
    fovLabel.Text = "FOV: " .. fovSize
end)

-- 7. Team Check切り替えボタン
local teamButton = Instance.new("TextButton")
teamButton.Size = UDim2.new(0, 180, 0, 28)
teamButton.Position = UDim2.new(0, 15, 0, 184)
teamButton.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
teamButton.TextColor3 = Color3.fromRGB(255, 255, 255)
teamButton.Text = "Team Check: ON"
teamButton.TextSize = 12
teamButton.Font = Enum.Font.SourceSans
teamButton.Parent = mainFrame
Instance.new("UICorner", teamButton).CornerRadius = UDim.new(0, 5)

teamButton.MouseButton1Click:Connect(function()
    teamCheck = not teamCheck
    teamButton.Text = teamCheck and "Team Check: ON" or "Team Check: OFF"
end)

-- 8. 足元円表示の切り替えボタン
local radiusVisualButton = Instance.new("TextButton")
radiusVisualButton.Size = UDim2.new(0, 180, 0, 28)
radiusVisualButton.Position = UDim2.new(0, 15, 0, 217)
radiusVisualButton.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
radiusVisualButton.TextColor3 = Color3.fromRGB(255, 255, 255)
radiusVisualButton.Text = "Radius Circle: ON"
radiusVisualButton.TextSize = 12
radiusVisualButton.Font = Enum.Font.SourceSans
radiusVisualButton.Parent = mainFrame
Instance.new("UICorner", radiusVisualButton).CornerRadius = UDim.new(0, 5)

-- 9. 左下に画像を追加するパーツ
local qrImageLabel = Instance.new("ImageLabel")
qrImageLabel.Size = UDim2.new(0, 55, 0, 55)
qrImageLabel.Position = UDim2.new(0, 15, 1, -68)
qrImageLabel.BackgroundTransparency = 1
qrImageLabel.Image = "rbxassetid://0" -- 必要に応じてご自身の画像IDに変更してください
qrImageLabel.Parent = mainFrame
Instance.new("UICorner", qrImageLabel).CornerRadius = UDim.new(0, 5)

-- 10. 閉じるボタン (X)
local closeButton = Instance.new("TextButton")
closeButton.Size = UDim2.new(0, 30, 0, 30)
closeButton.Position = UDim2.new(1, -38, 1, -38)
closeButton.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
closeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
closeButton.Text = "X"
closeButton.TextSize = 14
closeButton.Font = Enum.Font.SourceSansBold
closeButton.Parent = mainFrame
Instance.new("UICorner", closeButton).CornerRadius = UDim.new(0, 5)

openButton.MouseButton1Click:Connect(function()
    mainFrame.Visible = not mainFrame.Visible
end)
closeButton.MouseButton1Click:Connect(function()
    mainFrame.Visible = false
end)

-- ==========================================
-- 無限ジャンプの入力処理
-- ==========================================
UserInputService.JumpRequest:Connect(function()
    if infinityJumpEnabled and localPlayer.Character then
        local rootPart = localPlayer.Character:FindFirstChild("HumanoidRootPart")
        if rootPart then
            rootPart.Velocity = Vector3.new(rootPart.Velocity.X, 35, rootPart.Velocity.Z)
        end
    end
end)

-- FOV円の描画処理（画面上の照準用）
local fovCircleGui = Instance.new("Frame")
fovCircleGui.Name = "FOVCircleVisual"
fovCircleGui.BackgroundTransparency = 1
fovCircleGui.AnchorPoint = Vector2.new(0.5, 0.5)
fovCircleGui.Position = UDim2.new(0.5, 0, 0.5, 0)
fovCircleGui.Parent = screenGui

local uiStroke = Instance.new("UIStroke")
uiStroke.Color = Color3.fromRGB(255, 255, 255)
uiStroke.Thickness = 1.5
uiStroke.Transparency = 0.3
uiStroke.Parent = fovCircleGui

local uiCorner = Instance.new("UICorner")
uiCorner.CornerRadius = UDim.new(1, 0)
uiCorner.Parent = fovCircleGui

-- ==========================================
-- プレイヤーの足元に「すこーし透ける白円」を表示するパーツ
-- ==========================================
local radiusPart = Instance.new("Part")
radiusPart.Name = "RadiusVisualCircle"
radiusPart.Shape = Enum.PartType.Cylinder
radiusPart.Size = Vector3.new(0.1, 72, 72) -- 半径36スタッド（約10メートル）×2 = 直径72スタッド
radiusPart.Anchored = true
radiusPart.CanCollide = false
radiusPart.CastShadow = false
radiusPart.Transparency = 0.85 -- すこーし透けるくらい（0に近づくほど濃くなり、1に近づくと透明になります）
radiusPart.Color = Color3.fromRGB(255, 255, 255) -- 白色の円
radiusPart.Material = Enum.Material.Neon
radiusPart.Parent = workspace

-- 白いふち（輪郭線）を強調するためのSelectionBox
local selectionBox = Instance.new("SelectionBox")
selectionBox.Adornee = radiusPart
selectionBox.Color3 = Color3.fromRGB(255, 255, 255) -- 白色のふち
selectionBox.LineThickness = 0.08
selectionBox.SurfaceTransparency = 1
selectionBox.Parent = radiusPart

-- ボタンを押した時のオンオフ切り替え処理
radiusVisualButton.MouseButton1Click:Connect(function()
    radiusVisualEnabled = not radiusVisualEnabled
    radiusVisualButton.Text = radiusVisualEnabled and "Radius Circle: ON" or "Radius Circle: OFF"
    if not radiusVisualEnabled then
        radiusPart.Position = Vector3.new(0, -999, 0)
    end
end)

-- 視線・壁チェック関数（障害物に阻まれていないか）
local function isVisible(targetPart)
    local origin = camera.CFrame.Position
    local direction = (targetPart.Position - origin)
    local raycastParams = RaycastParams.new()
    raycastParams.FilterType = Enum.RaycastFilterType.Blacklist
    raycastParams.FilterDescendantsInstances = {localPlayer.Character}
    
    local raycastResult = workspace:Raycast(origin, direction, raycastParams)
    if raycastResult then
        if raycastResult.Instance:IsDescendantOf(targetPart.Parent) then
            return true
        end
        return false
    end
    return true
end

-- ターゲット選定関数
local function getTarget()
    local myRoot = localPlayer.Character and localPlayer.Character:FindFirstChild("HumanoidRootPart")
    local screenCenter = Vector2.new(camera.ViewportSize.X / 2, camera.ViewportSize.Y / 2)
    
    local closestPlayer = nil
    local shortestDistance = 999999

    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= localPlayer and player.Character then
            if teamCheck and player.Team == localPlayer.Team then continue end

            local humanoid = player.Character:FindFirstChildOfClass("Humanoid")
            local head = player.Character:FindFirstChild("Head")
            local targetRoot = player.Character:FindFirstChild("HumanoidRootPart")

            if humanoid and humanoid.Health > 0 and head and targetRoot then
                if isVisible(head) then
                    -- 条件A: 半径10メートル以内（約36スタッド）なら後ろの敵でも最優先
                    if myRoot then
                        local worldDist = (targetRoot.Position - myRoot.Position).Magnitude
                        if worldDist <= 36 then
                            if worldDist < shortestDistance then
                                shortestDistance = worldDist
                                closestPlayer = player
                            end
                        end
                    end

                    -- 条件B: 通常のFOV内オートエイム
                    if not closestPlayer then
                        local screenPoint, onScreen = camera:WorldToViewportPoint(head.Position)
                        if onScreen then
                            local magnitude = (Vector2.new(screenPoint.X, screenPoint.Y) - screenCenter).Magnitude
                            if magnitude < fovSize then
                                if magnitude < shortestDistance then
                                    shortestDistance = magnitude
                                    closestPlayer = player
                                end
                            end
                        end
                    end
                end
            end
        end
    end

    return closestPlayer
end

-- メインループ
RunService.RenderStepped:Connect(function()
    local diameter = fovSize * 2
    fovCircleGui.Size = UDim2.new(0, diameter, 0, diameter)

    if localPlayer.Character and localPlayer.Character:FindFirstChildOfClass("Humanoid") then
        localPlayer.Character:FindFirstChildOfClass("Humanoid").WalkSpeed = 40
        
        -- 足元の円をプレイヤーの位置に追従させる（ONのときのみ）
        local rootPart = localPlayer.Character:FindFirstChild("HumanoidRootPart")
        if rootPart then
            if radiusVisualEnabled then
                radiusPart.CFrame = rootPart.CFrame * CFrame.new(0, -2.5, 0) * CFrame.Angles(0, 0, math.rad(90))
            else
                radiusPart.Position = Vector3.new(0, -999, 0)
            end
        end
    else
        radiusPart.Position = Vector3.new(0, -999, 0) -- キャラクターがいない時は隠す
    end

    -- オートエイムの処理
    if aimbotEnabled then
        local target = getTarget()
        if target and target.Character and target.Character:FindFirstChild("Head") then
            local head = target.Character.Head
            camera.CFrame = CFrame.new(camera.CFrame.Position, head.Position)
        end
    end
end)
