local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local PlayerGui = Players.LocalPlayer:WaitForChild("PlayerGui")

-- 创建UI容器
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "CharacterStatsUI"
screenGui.Parent = PlayerGui

-- 主面板（可缩放）
local mainFrame = Instance.new("Frame")
mainFrame.Name = "MainFrame"
mainFrame.Size = UDim2.new(0, 350, 0, 450)
mainFrame.Position = UDim2.new(0.1, 0, 0.1, 0)
mainFrame.BackgroundColor3 = Color3.new(0.15, 0.15, 0.15)
mainFrame.BorderSizePixel = 2
mainFrame.BorderColor3 = Color3.new(0.5, 0.5, 0.5)
mainFrame.Parent = screenGui

-- 标题栏
local titleBar = Instance.new("Frame")
titleBar.Size = UDim2.new(1, 0, 0, 30)
titleBar.BackgroundColor3 = Color3.new(0.3, 0.3, 0.3)
titleBar.Parent = mainFrame

local titleLabel = Instance.new("TextLabel")
titleLabel.Size = UDim2.new(0.8, 0, 1, 0)
titleLabel.Position = UDim2.new(0.1, 0, 0, 0)
titleLabel.Text = "角色属性调节器"
titleLabel.TextColor3 = Color3.new(1, 1, 1)
titleLabel.TextScaled = true
titleLabel.Parent = titleBar

-- 缩放按钮
local scaleBtn = Instance.new("TextButton")
scaleBtn.Size = UDim2.new(0, 25, 0, 25)
scaleBtn.Position = UDim2.new(1, -30, 0, 2.5)
scaleBtn.Text = "⇱"
scaleBtn.TextColor3 = Color3.new(1, 1, 1)
scaleBtn.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)
scaleBtn.Parent = titleBar

-- 内容容器
local contentContainer = Instance.new("Frame")
contentContainer.Size = UDim2.new(1, 0, 1, -30)
contentContainer.Position = UDim2.new(0, 0, 0, 30)
contentContainer.BackgroundTransparency = 1
contentContainer.Parent = mainFrame

-- 数值范围配置
local config = {
    health = {min = 0, max = 500, default = 100, step = 50},
    speed = {min = 0, max = 500, default = 16, step = 10},
    jump = {min = 0, max = 500, default = 50, step = 10}
}

-- 角色引用
local character = Players.LocalPlayer.Character or Players.LocalPlayer.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")
local rootPart = character:WaitForChild("HumanoidRootPart")

-- 无限跳跃开关状态
local isInfiniteJump = false

-- 创建属性调节区域（复用函数）
local function createStatControl(name, yPos)
    -- 区域容器
    local controlFrame = Instance.new("Frame")
    controlFrame.Size = UDim2.new(1, -20, 0, 100)
    controlFrame.Position = UDim2.new(0, 10, 0, yPos)
    controlFrame.BackgroundTransparency = 1
    controlFrame.Parent = contentContainer

    -- 标签
    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(1, 0, 0, 20)
    label.Position = UDim2.new(0, 0, 0, 10)
    label.Text = string.format("%s: %d / %d", 
        name == "health" and "健康值" or name == "speed" and "移动速度" or "跳跃力",
        config[name].default, config[name].max
    )
    label.TextColor3 = Color3.new(1, 1, 1)
    label.Parent = controlFrame

    -- 滑块
    local slider = Instance.new("ScrollingFrame")
    slider.Name = name .. "Slider"
    slider.Size = UDim2.new(1, 0, 0, 10)
    slider.Position = UDim2.new(0, 0, 0, 40)
    slider.BackgroundColor3 = Color3.new(0.3, 0.3, 0.3)
    slider.ScrollBarThickness = 10
    slider.Parent = controlFrame

    -- 滑块填充色
    local fillColor = name == "health" and Color3.new(0, 1, 0) 
        or name == "speed" and Color3.new(0, 0.7, 1) 
        or Color3.new(1, 0.5, 0)
    local sliderFill = Instance.new("Frame")
    sliderFill.Size = UDim2.new(
        (config[name].default - config[name].min) / (config[name].max - config[name].min), 
        0, 1, 0
    )
    sliderFill.BackgroundColor3 = fillColor
    sliderFill.Parent = slider

    -- 按钮
    local addBtn = Instance.new("TextButton")
    addBtn.Size = UDim2.new(0.4, 0, 0, 30)
    addBtn.Position = UDim2.new(0, 0, 0, 60)
    addBtn.Text = "增加"
    addBtn.TextColor3 = Color3.new(1, 1, 1)
    addBtn.BackgroundColor3 = Color3.new(0.2, 0.4, 0.2)
    addBtn.Parent = controlFrame

    local minusBtn = Instance.new("TextButton")
    minusBtn.Size = UDim2.new(0.4, 0, 0, 30)
    minusBtn.Position = UDim2.new(0.6, 0, 0, 60)
    minusBtn.Text = "减少"
    minusBtn.TextColor3 = Color3.new(1, 1, 1)
    minusBtn.BackgroundColor3 = Color3.new(0.4, 0.2, 0.2)
    minusBtn.Parent = controlFrame

    -- 更新数值函数
    local function updateValue(newValue)
        local clampedValue = math.clamp(newValue, config[name].min, config[name].max)
        if name == "health" then
            humanoid.Health = clampedValue
            humanoid.MaxHealth = config.health.max
        elseif name == "speed" then
            humanoid.WalkSpeed = clampedValue
        elseif name == "jump" then
            humanoid.JumpPower = clampedValue
        end
        label.Text = string.format("%s: %.0f / %d", 
            name == "health" and "健康值" or name == "speed" and "移动速度" or "跳跃力",
            clampedValue, config[name].max
        )
        local percentage = (clampedValue - config[name].min) / (config[name].max - config[name].min)
        sliderFill.Size = UDim2.new(percentage, 0, 1, 0)
        slider.CanvasPosition = Vector2.new(percentage * slider.AbsoluteWindowSize.X, 0)
    end

    -- 绑定事件
    slider:GetPropertyChangedSignal("CanvasPosition"):Connect(function()
        local percentage = math.clamp(slider.CanvasPosition.X / slider.AbsoluteWindowSize.X, 0, 1)
        updateValue(config[name].min + percentage * (config[name].max - config[name].min))
    end)
    addBtn.MouseButton1Click:Connect(function()
        updateValue((name == "health" and humanoid.Health or name == "speed" and humanoid.WalkSpeed or humanoid.JumpPower) + config[name].step)
    end)
    minusBtn.MouseButton1Click:Connect(function()
        updateValue((name == "health" and humanoid.Health or name == "speed" and humanoid.WalkSpeed or humanoid.JumpPower) - config[name].step)
    end)

    updateValue(config[name].default)
end

-- 创建三个属性调节区域
createStatControl("health", 10)
createStatControl("speed", 120)
createStatControl("jump", 230)

-- 无限跳跃开关UI
local infiniteJumpFrame = Instance.new("Frame")
infiniteJumpFrame.Size = UDim2.new(1, -20, 0, 50)
infiniteJumpFrame.Position = UDim2.new(0, 10, 0, 340)
infiniteJumpFrame.BackgroundTransparency = 1
infiniteJumpFrame.Parent = contentContainer

local jumpLabel = Instance.new("TextLabel")
jumpLabel.Size = UDim2.new(0.6, 0, 0, 30)
jumpLabel.Position = UDim2.new(0, 0, 0, 10)
jumpLabel.Text = "无限跳跃"
jumpLabel.TextColor3 = Color3.new(1, 1, 1)
jumpLabel.Parent = infiniteJumpFrame

local jumpToggle = Instance.new("TextButton")
jumpToggle.Size = UDim2.new(0.3, 0, 0, 30)
jumpToggle.Position = UDim2.new(0.7, 0, 0, 10)
jumpToggle.Text = "关闭"
jumpToggle.TextColor3 = Color3.new(1, 1, 1)
jumpToggle.BackgroundColor3 = Color3.new(0.4, 0.2, 0.2)
jumpToggle.Parent = infiniteJumpFrame

-- 无限跳跃逻辑
local function handleInfiniteJump(input, gameProcessed)
    if not gameProcessed and isInfiniteJump then
        if input.KeyCode == Enum.KeyCode.Space or input.UserInputType == Enum.UserInputType.Touch then
            humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
        end
    end
end

-- 开关事件
jumpToggle.MouseButton1Click:Connect(function()
    isInfiniteJump = not isInfiniteJump
    if isInfiniteJump then
        jumpToggle.Text = "开启"
        jumpToggle.BackgroundColor3 = Color3.new(0.2, 0.4, 0.2)
        UserInputService.InputBegan:Connect(handleInfiniteJump)
    else
        jumpToggle.Text = "关闭"
        jumpToggle.BackgroundColor3 = Color3.new(0.4, 0.2, 0.2)
    end
end)

-- UI缩放功能
local isExpanded = true
local originalSize = mainFrame.Size

scaleBtn.MouseButton1Click:Connect(function()
    isExpanded = not isExpanded
    mainFrame.Size = isExpanded and originalSize or UDim2.new(0, 150, 0, 30)
    contentContainer.Visible = isExpanded
    scaleBtn.Text = isExpanded and "⇱" or "⇲"
end)

-- 角色重生时重新绑定
Players.LocalPlayer.CharacterAdded:Connect(function(newChar)
    character = newChar
    humanoid = newChar:WaitForChild("Humanoid")
    rootPart = newChar:WaitForChild("HumanoidRootPart")
    humanoid.MaxHealth = config.health.max
end)
