-- BhBz Hub v11.0 - Red Edition (Stable)
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Lighting = game:GetService("Lighting")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera
local Mouse = LocalPlayer:GetMouse()

-- CONFIGURAÇÕES
local Settings = {
    Aimbot = false,
    NoRecoil = false,
    ESP = false,
    Spinbot = false,
    NoClip = false,
    WalkSpeed = 16,
    JumpPower = 50,
    FullBright = false,
    AimPart = "Head",
    FOV = 100,
    ESPColor = Color3.fromRGB(255, 0, 0), -- Vermelho
    InfJump = false
}

-- INTERFACE PRINCIPAL
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "BhBz_Red_Edition"
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")
ScreenGui.ResetOnSpawn = false
ScreenGui.DisplayOrder = 999

local MainFrame = Instance.new("Frame", ScreenGui)
MainFrame.Size = UDim2.new(0, 500, 0, 320)
MainFrame.Position = UDim2.new(0.5, -250, 0.5, -160)
MainFrame.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
MainFrame.BorderSizePixel = 0
MainFrame.Active = true

-- ARRASTAR O HUB (DRAG SYSTEM)
local dragging, dragInput, dragStart, startPos
MainFrame.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = input.Position
        startPos = MainFrame.Position
        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then dragging = false end
        end)
    end
end)
MainFrame.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement then dragInput = input end
end)
RunService.RenderStepped:Connect(function()
    if dragging and dragInput then
        local delta = dragInput.Position - dragStart
        MainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)

Instance.new("UICorner", MainFrame).CornerRadius = UDim.new(0, 10)

-- Lateral
local SideBar = Instance.new("Frame", MainFrame)
SideBar.Size = UDim2.new(0, 130, 1, 0)
SideBar.BackgroundColor3 = Color3.fromRGB(10, 10, 10)
Instance.new("UICorner", SideBar).CornerRadius = UDim.new(0, 10)

local Title = Instance.new("TextLabel", SideBar)
Title.Size = UDim2.new(1, 0, 0, 50)
Title.Text = "BhBz Hub"
Title.TextColor3 = Color3.fromRGB(255, 0, 0) -- Título Vermelho
Title.Font = Enum.Font.GothamBold
Title.TextSize = 20
Title.BackgroundTransparency = 1

local Content = Instance.new("Frame", MainFrame)
Content.Size = UDim2.new(1, -140, 1, -10)
Content.Position = UDim2.new(0, 135, 0, 5)
Content.BackgroundTransparency = 1

local Tabs = {
    Combat = Instance.new("ScrollingFrame", Content),
    Visual = Instance.new("ScrollingFrame", Content),
    Player = Instance.new("ScrollingFrame", Content)
}

for _, tab in pairs(Tabs) do
    tab.Size = UDim2.new(1, 0, 1, 0); tab.BackgroundTransparency = 1; tab.Visible = false
    tab.ScrollBarThickness = 0; tab.CanvasSize = UDim2.new(0, 0, 1.5, 0)
end
Tabs.Combat.Visible = true

local btnY = 60
local function CreateTabBtn(name, tabFrame)
    local btn = Instance.new("TextButton", SideBar)
    btn.Size = UDim2.new(0.85, 0, 0, 35); btn.Position = UDim2.new(0.07, 0, 0, btnY)
    btn.Text = name; btn.BackgroundColor3 = Color3.fromRGB(20, 20, 20); btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.Font = Enum.Font.GothamBold; btn.TextSize = 13
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 6)
    
    btn.MouseButton1Click:Connect(function()
        for _, t in pairs(Tabs) do t.Visible = false end
        tabFrame.Visible = true
    end)
    btnY = btnY + 40
end

CreateTabBtn("Combat", Tabs.Combat)
CreateTabBtn("Visual", Tabs.Visual)
CreateTabBtn("Player", Tabs.Player)

-- Toggle Vermelho (Estilo Redz)
local function AddToggle(parent, name, yPos, callback)
    local frame = Instance.new("Frame", parent)
    frame.Size = UDim2.new(0.95, 0, 0, 45); frame.Position = UDim2.new(0, 5, 0, yPos); frame.BackgroundColor3 = Color3.fromRGB(22, 22, 22)
    Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 8)
    
    local label = Instance.new("TextLabel", frame)
    label.Size = UDim2.new(1, -60, 1, 0); label.Position = UDim2.new(0, 15, 0, 0); label.Text = name
    label.TextColor3 = Color3.fromRGB(255, 255, 255); label.Font = Enum.Font.Gotham; label.TextSize = 14; label.TextXAlignment = Enum.TextXAlignment.Left; label.BackgroundTransparency = 1
    
    local btn = Instance.new("TextButton", frame)
    btn.Size = UDim2.new(0, 40, 0, 22); btn.Position = UDim2.new(1, -50, 0.5, -11); btn.BackgroundColor3 = Color3.fromRGB(40, 40, 40); btn.Text = ""
    local bc = Instance.new("UICorner", btn); bc.CornerRadius = UDim.new(1, 0)
    
    local dot = Instance.new("Frame", btn)
    dot.Size = UDim2.new(0, 16, 0, 16); dot.Position = UDim2.new(0, 3, 0.5, -8); dot.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    Instance.new("UICorner", dot).CornerRadius = UDim.new(1, 0)
    
    local enabled = false
    btn.MouseButton1Click:Connect(function()
        enabled = not enabled
        callback(enabled)
        if enabled then
            btn.BackgroundColor3 = Color3.fromRGB(200, 0, 0) -- Ativado em Vermelho
            dot:TweenPosition(UDim2.new(1, -19, 0.5, -8), "Out", "Quad", 0.15)
        else
            btn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
            dot:TweenPosition(UDim2.new(0, 3, 0.5, -8), "Out", "Quad", 0.15)
        end
    end)
end

-- FUNÇÕES
AddToggle(Tabs.Combat, "Aimbot (Botão Direito)", 10, function(v) Settings.Aimbot = v end)
AddToggle(Tabs.Combat, "No Recoil Estável", 60, function(v) Settings.NoRecoil = v end)
AddToggle(Tabs.Visual, "ESP (Highlight Fix)", 10, function(v) Settings.ESP = v end)
AddToggle(Tabs.Visual, "Full Bright", 60, function(v) Settings.FullBright = v end)
AddToggle(Tabs.Player, "Speed (100)", 10, function(v) Settings.WalkSpeed = v and 100 or 16 end)
AddToggle(Tabs.Player, "Jump (150)", 60, function(v) Settings.JumpPower = v and 150 or 50 end)
AddToggle(Tabs.Player, "NoClip", 110, function(v) Settings.NoClip = v end)

-- FOV CIRCLE
local fov = Drawing.new("Circle")
fov.Thickness = 2; fov.Color = Color3.fromRGB(255, 0, 0); fov.Visible = true

-- LÓGICA NO RECOIL (MÉTODO VELOCITY ZERO)
RunService.RenderStepped:Connect(function()
    if Settings.NoRecoil then
        -- Força a câmera a ignorar vetores de recuo comuns
        Camera.RotVelocity = Vector3.new(0,0,0)
    end
    
    if Settings.Aimbot and UserInputService:IsMouseButtonPressed(Enum.UserInputType.MouseButton2) then
        local t, d = nil, Settings.FOV
        for _, v in pairs(Players:GetPlayers()) do
            if v ~= LocalPlayer and v.Character and v.Character:FindFirstChild("Head") then
                local pos, on = Camera:WorldToViewportPoint(v.Character.Head.Position)
                if on then
                    local mD = (Vector2.new(Mouse.X, Mouse.Y) - Vector2.new(pos.X, pos.Y)).Magnitude
                    if mD < d then t = v.Character.Head; d = mD end
                end
            end
        end
        if t then Camera.CFrame = CFrame.new(Camera.CFrame.Position, t.Position) end
    end
end)

-- LÓGICA ESP E SISTEMAS
RunService.Heartbeat:Connect(function()
    fov.Radius = Settings.FOV; fov.Position = UserInputService:GetMouseLocation()
    
    if Settings.ESP then
        for _, p in pairs(Players:GetPlayers()) do
            if p ~= LocalPlayer and p.Character then
                local h = p.Character:FindFirstChild("BhBz_ESP") or Instance.new("Highlight")
                h.Name = "BhBz_ESP"
                h.Parent = p.Character
                h.FillColor = Settings.ESPColor
                h.OutlineColor = Color3.fromRGB(255, 255, 255)
                h.Enabled = true
            end
        end
    else
        for _, p in pairs(Players:GetPlayers()) do
            if p.Character and p.Character:FindFirstChild("BhBz_ESP") then
                p.Character.BhBz_ESP.Enabled = false
            end
        end
    end

    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        LocalPlayer.Character.Humanoid.WalkSpeed = Settings.WalkSpeed
        LocalPlayer.Character.Humanoid.JumpPower = Settings.JumpPower
        if Settings.NoClip then
            for _, part in pairs(LocalPlayer.Character:GetDescendants()) do
                if part:IsA("BasePart") then part.CanCollide = false end
            end
        end
    end
    if Settings.FullBright then Lighting.Brightness = 2; Lighting.ClockTime = 14; Lighting.GlobalShadows = false end
end)

UserInputService.InputBegan:Connect(function(i, gp)
    if not gp and i.KeyCode == Enum.KeyCode.LeftControl then MainFrame.Visible = not MainFrame.Visible end
end)

print("BhBz Hub v11 Carregado!")
