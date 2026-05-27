local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Lighting = game:GetService("Lighting")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera
local Mouse = LocalPlayer:GetMouse()

local Settings = {
    Aimbot = false,
    NoRecoil = false,
    ESP = false,
    Spinbot = false,
    NoClip = false,
    WalkSpeed = 16,
    JumpPower = 50,
    FullBright = false,
    HitboxSize = 2, 
    HitboxEnabled = false,
    AimPart = "Head",
    FOV = 100,
    ESPColor = Color3.fromRGB(255, 0, 0),
    FlyEnabled = false,
    FlySpeed = 50
}

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "BhBz_Red_v13_0"
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")
ScreenGui.ResetOnSpawn = false
ScreenGui.DisplayOrder = 999

local MainFrame = Instance.new("Frame", ScreenGui)
MainFrame.Size = UDim2.new(0, 500, 0, 350)
MainFrame.Position = UDim2.new(0.5, -250, 0.5, -175)
MainFrame.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
MainFrame.BorderSizePixel = 0
MainFrame.Active = true

local dragging, dragInput, dragStart, startPos
MainFrame.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true; dragStart = input.Position; startPos = MainFrame.Position
        input.Changed:Connect(function() if input.UserInputState == Enum.UserInputState.End then dragging = false end end)
    end
end)
MainFrame.InputChanged:Connect(function(input) if input.UserInputType == Enum.UserInputType.MouseMovement then dragInput = input end end)
RunService.RenderStepped:Connect(function()
    if dragging and dragInput then
        local delta = dragInput.Position - dragStart
        MainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)

Instance.new("UICorner", MainFrame).CornerRadius = UDim.new(0, 10)

local SideBar = Instance.new("Frame", MainFrame)
SideBar.Size = UDim2.new(0, 130, 1, 0); SideBar.BackgroundColor3 = Color3.fromRGB(10, 10, 10)
Instance.new("UICorner", SideBar).CornerRadius = UDim.new(0, 10)

local Title = Instance.new("TextLabel", SideBar)
Title.Size = UDim2.new(1, 0, 0, 50); Title.Text = "BhBz Hub"; Title.TextColor3 = Color3.fromRGB(255, 0, 0); Title.Font = Enum.Font.GothamBold; Title.TextSize = 20; Title.BackgroundTransparency = 1

local Content = Instance.new("Frame", MainFrame)
Content.Size = UDim2.new(1, -140, 1, -10); Content.Position = UDim2.new(0, 135, 0, 5); Content.BackgroundTransparency = 1

local Tabs = { Combat = Instance.new("ScrollingFrame", Content), Visual = Instance.new("ScrollingFrame", Content), Player = Instance.new("ScrollingFrame", Content) }
for _, tab in pairs(Tabs) do
    tab.Size = UDim2.new(1, 0, 1, 0); tab.BackgroundTransparency = 1; tab.Visible = false; tab.ScrollBarThickness = 0; tab.CanvasSize = UDim2.new(0, 0, 2, 0)
end
Tabs.Combat.Visible = true

local btnY = 60
local function CreateTabBtn(name, tabFrame)
    local btn = Instance.new("TextButton", SideBar)
    btn.Size = UDim2.new(0.85, 0, 0, 35); btn.Position = UDim2.new(0.07, 0, 0, btnY); btn.Text = name; btn.BackgroundColor3 = Color3.fromRGB(20, 20, 20); btn.TextColor3 = Color3.fromRGB(255, 255, 255); btn.Font = Enum.Font.GothamBold; btn.TextSize = 13; Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 6)
    btn.MouseButton1Click:Connect(function() for _, t in pairs(Tabs) do t.Visible = false end; tabFrame.Visible = true end)
    btnY = btnY + 40
end
CreateTabBtn("Combat", Tabs.Combat); CreateTabBtn("Visual", Tabs.Visual); CreateTabBtn("Player", Tabs.Player)

local function AddToggle(parent, name, yPos, callback)
    local frame = Instance.new("Frame", parent)
    frame.Size = UDim2.new(0.95, 0, 0, 45); frame.Position = UDim2.new(0, 5, 0, yPos); frame.BackgroundColor3 = Color3.fromRGB(22, 22, 22); Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 8)
    local label = Instance.new("TextLabel", frame); label.Size = UDim2.new(1, -60, 1, 0); label.Position = UDim2.new(0, 15, 0, 0); label.Text = name; label.TextColor3 = Color3.fromRGB(255, 255, 255); label.Font = Enum.Font.Gotham; label.TextSize = 14; label.TextXAlignment = Enum.TextXAlignment.Left; label.BackgroundTransparency = 1
    local btn = Instance.new("TextButton", frame); btn.Size = UDim2.new(0, 40, 0, 22); btn.Position = UDim2.new(1, -50, 0.5, -11); btn.BackgroundColor3 = Color3.fromRGB(40, 40, 40); btn.Text = ""; Instance.new("UICorner", btn).CornerRadius = UDim.new(1, 0)
    local dot = Instance.new("Frame", btn); dot.Size = UDim2.new(0, 16, 0, 16); dot.Position = UDim2.new(0, 3, 0.5, -8); dot.BackgroundColor3 = Color3.fromRGB(255, 255, 255); Instance.new("UICorner", dot).CornerRadius = UDim.new(1, 0)
    local enabled = false
    btn.MouseButton1Click:Connect(function() enabled = not enabled; callback(enabled); btn.BackgroundColor3 = enabled and Color3.fromRGB(200, 0, 0) or Color3.fromRGB(40, 40, 40); dot:TweenPosition(UDim2.new(enabled and 1 or 0, enabled and -19 or 3, 0.5, -8), "Out", "Quad", 0.15) end)
end

local function AddSlider(parent, name, yPos, min, max, callback)
    local frame = Instance.new("Frame", parent)
    frame.Size = UDim2.new(0.95, 0, 0, 50); frame.Position = UDim2.new(0, 5, 0, yPos); frame.BackgroundColor3 = Color3.fromRGB(22, 22, 22); Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 8)
    local label = Instance.new("TextLabel", frame); label.Size = UDim2.new(1, 0, 0, 20); label.Position = UDim2.new(0, 15, 0, 5); label.Text = name .. ": " .. min; label.TextColor3 = Color3.fromRGB(255, 255, 255); label.Font = Enum.Font.Gotham; label.TextSize = 12; label.TextXAlignment = Enum.TextXAlignment.Left; label.BackgroundTransparency = 1
    local bar = Instance.new("Frame", frame); bar.Size = UDim2.new(0.85, 0, 0, 4); bar.Position = UDim2.new(0.07, 0, 0, 35); bar.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    local slider = Instance.new("TextButton", bar); slider.Size = UDim2.new(0, 12, 0, 12); slider.Position = UDim2.new(0, 0, 0.5, -6); slider.BackgroundColor3 = Color3.fromRGB(255, 0, 0); slider.Text = ""
    Instance.new("UICorner", slider).CornerRadius = UDim.new(1, 0)
    
    local draggingSlider = false
    slider.MouseButton1Down:Connect(function() draggingSlider = true end)
    UserInputService.InputEnded:Connect(function(input) if input.UserInputType == Enum.UserInputType.MouseButton1 then draggingSlider = false end end)
    
    RunService.RenderStepped:Connect(function()
        if draggingSlider then
            local mousePos = UserInputService:GetMouseLocation().X
            local barPos = bar.AbsolutePosition.X
            local barWidth = bar.AbsoluteSize.X
            local percent = math.clamp((mousePos - barPos) / barWidth, 0, 1)
            slider.Position = UDim2.new(percent, -6, 0.5, -6)
            local value = math.floor(min + (max - min) * percent)
            label.Text = name .. ": " .. value
            callback(value)
        end
    end)
end

AddToggle(Tabs.Combat, "Aimbot (Segurar M2)", 10, function(v) Settings.Aimbot = v end)
AddToggle(Tabs.Combat, "No Recoil Clássico", 60, function(v) Settings.NoRecoil = v end)
AddToggle(Tabs.Combat, "Ativar Hitbox Head", 110, function(v) Settings.HitboxEnabled = v end)
AddSlider(Tabs.Combat, "Tamanho da Hitbox", 160, 1, 20, function(v) Settings.HitboxSize = v end)

AddToggle(Tabs.Visual, "ESP Inimigos", 10, function(v) Settings.ESP = v end)
AddToggle(Tabs.Visual, "Full Bright", 60, function(v) Settings.FullBright = v end)

AddToggle(Tabs.Player, "Spinbot", 10, function(v) Settings.Spinbot = v end)
AddToggle(Tabs.Player, "Velocidade (350)", 60, function(v) Settings.WalkSpeed = v and 350 or 16 end)
AddToggle(Tabs.Player, "Pulo (150)", 110, function(v) Settings.JumpPower = v and 150 or 50 end)
AddToggle(Tabs.Player, "NoClip", 160, function(v) Settings.NoClip = v end)
AddToggle(Tabs.Player, "Ativar Voo (Fly)", 210, function(v) Settings.FlyEnabled = v end)
AddSlider(Tabs.Player, "Velocidade do Voo", 260, 1, 300, function(v) Settings.FlySpeed = v end)

local LastRotation = Camera.CFrame
RunService.RenderStepped:Connect(function()
    if Settings.NoRecoil and UserInputService:IsMouseButtonPressed(Enum.UserInputType.MouseButton1) then
        local DeltaX, DeltaY, DeltaZ = Camera.CFrame:ToOrientation()
        local LastX = LastRotation:ToOrientation()
        if DeltaX > LastX then Camera.CFrame = CFrame.new(Camera.CFrame.Position) * CFrame.fromOrientation(LastX, DeltaY, DeltaZ) end
    end
    LastRotation = Camera.CFrame
    
    if Settings.Aimbot and UserInputService:IsMouseButtonPressed(Enum.UserInputType.MouseButton2) then
        local target = nil; local dist = Settings.FOV
        for _, v in pairs(Players:GetPlayers()) do
            if v ~= LocalPlayer and v.Character and v.Character:FindFirstChild("Head") then
                local pos, onScreen = Camera:WorldToViewportPoint(v.Character.Head.Position)
                if onScreen then
                    local mouseDist = (Vector2.new(Mouse.X, Mouse.Y) - Vector2.new(pos.X, pos.Y)).Magnitude
                    if mouseDist < dist then target = v.Character.Head; dist = mouseDist end
                end
            end
        end
        if target then Camera.CFrame = CFrame.new(Camera.CFrame.Position, target.Position) end
    end
end)

RunService.Heartbeat:Connect(function()
    pcall(function()
        local char = LocalPlayer.Character
        local hum = char and char:FindFirstChildOfClass("Humanoid")
        local hrp = char and char:FindFirstChild("HumanoidRootPart")
        
        if hum and hrp then
            if Settings.FlyEnabled then
                hum.PlatformStand = true
                local flyVelocity = Vector3.new(0, 0, 0)
                
                if UserInputService:IsKeyDown(Enum.KeyCode.W) then flyVelocity = flyVelocity + Camera.CFrame.LookVector end
                if UserInputService:IsKeyDown(Enum.KeyCode.S) then flyVelocity = flyVelocity - Camera.CFrame.LookVector end
                if UserInputService:IsKeyDown(Enum.KeyCode.A) then flyVelocity = flyVelocity - Camera.CFrame.RightVector end
                if UserInputService:IsKeyDown(Enum.KeyCode.D) then flyVelocity = flyVelocity + Camera.CFrame.RightVector end
                if UserInputService:IsKeyDown(Enum.KeyCode.Space) then flyVelocity = flyVelocity + Vector3.new(0, 1, 0) end
                if UserInputService:IsKeyDown(Enum.KeyCode.LeftShift) then flyVelocity = flyVelocity - Vector3.new(0, 1, 0) end
                
                hrp.Velocity = flyVelocity.Unit * Settings.FlySpeed
                if flyVelocity == Vector3.new(0, 0, 0) then hrp.Velocity = Vector3.new(0, 0, 0) end
            else
                if hum.PlatformStand then hum.PlatformStand = false end
                hum.WalkSpeed = Settings.WalkSpeed
                hum.JumpPower = Settings.JumpPower
                if Settings.Spinbot then hum.AutoRotate = false; hrp.CFrame *= CFrame.Angles(0, math.rad(60), 0) else hum.AutoRotate = true end
            end
            
            if Settings.NoClip then for _, p in pairs(char:GetDescendants()) do if p:IsA("BasePart") then p.CanCollide = false end end end
        end

        for _, p in pairs(Players:GetPlayers()) do
            if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild("Head") then
                local head = p.Character.Head
                if Settings.HitboxEnabled then
                    head.Size = Vector3.new(Settings.HitboxSize, Settings.HitboxSize, Settings.HitboxSize)
                    head.Transparency = 0.7; head.Color = Color3.fromRGB(255, 0, 0); head.CanCollide = false
                else
                    head.Size = Vector3.new(1.2, 1.2, 1.2); head.Transparency = 0
                end
            end
        end

        if Settings.ESP then
            for _, p in pairs(Players:GetPlayers()) do
                if p ~= LocalPlayer and p.Character then
                    local h = p.Character:FindFirstChild("BhBz_ESP") or Instance.new("Highlight", p.Character)
                    h.Name = "BhBz_ESP"; h.FillColor = Settings.ESPColor; h.Enabled = true
                end
            end
        end
        if Settings.FullBright then Lighting.Brightness = 2; Lighting.GlobalShadows = false end
    end)
end)

UserInputService.InputBegan:Connect(function(i, gp)
    if not gp and i.KeyCode == Enum.KeyCode.LeftControl then MainFrame.Visible = not MainFrame.Visible end
end)

print("BhBz Hub v13.0 Loaded!")
