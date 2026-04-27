-- BhBz Hub - Official Script
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera
local Mouse = LocalPlayer:GetMouse()

-- CONFIGURAÇÕES
local Settings = {
    Aimbot = false,
    ESP = false,
    AimPart = "Head",
    FOV = 100,
    TeamCheck = false
}

-- INTERFACE DO MENU
local ScreenGui = Instance.new("ScreenGui", LocalPlayer:WaitForChild("PlayerGui"))
ScreenGui.Name = "BhBz_Hub"

local MainFrame = Instance.new("Frame", ScreenGui)
MainFrame.Size = UDim2.new(0, 250, 0, 350)
MainFrame.Position = UDim2.new(0.5, -125, 0.5, -175)
MainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
MainFrame.Active = true
MainFrame.Draggable = true

local Title = Instance.new("TextLabel", MainFrame)
Title.Size = UDim2.new(1, 0, 0, 40)
Title.Text = "BhBz Hub"
Title.TextColor3 = Color3.fromRGB(0, 255, 255)
Title.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
Title.Font = Enum.Font.GothamBold
Title.TextSize = 20

-- FUNÇÃO DE TOGGLE (BOTAO)
local function CreateButton(name, pos, callback)
    local Btn = Instance.new("TextButton", MainFrame)
    Btn.Size = UDim2.new(0.8, 0, 0, 35)
    Btn.Position = pos
    Btn.Text = name .. ": OFF"
    Btn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    Btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    
    local enabled = false
    Btn.MouseButton1Click:Connect(function()
        enabled = not enabled
        Btn.Text = name .. ": " .. (enabled and "ON" or "OFF")
        Btn.BackgroundColor3 = enabled and Color3.fromRGB(0, 150, 255) or Color3.fromRGB(50, 50, 50)
        callback(enabled)
    end)
end

CreateButton("Aimbot", UDim2.new(0.1, 0, 0, 60), function(v) Settings.Aimbot = v end)
CreateButton("ESP (Muralha)", UDim2.new(0.1, 0, 0, 105), function(v) Settings.ESP = v end)

-- SELETOR CORPO (CABEÇA/BARRIGA)
local PartBtn = Instance.new("TextButton", MainFrame)
PartBtn.Size = UDim2.new(0.8, 0, 0, 35)
PartBtn.Position = UDim2.new(0.1, 0, 0, 150)
PartBtn.Text = "Alvo: Cabeça"
PartBtn.BackgroundColor3 = Color3.fromRGB(70, 70, 70)
PartBtn.TextColor3 = Color3.fromRGB(255, 255, 255)

PartBtn.MouseButton1Click:Connect(function()
    if Settings.AimPart == "Head" then
        Settings.AimPart = "HumanoidRootPart"
        PartBtn.Text = "Alvo: Barriga"
    else
        Settings.AimPart = "Head"
        PartBtn.Text = "Alvo: Cabeça"
    end
end)

-- FOV VISUAL
local FOVCircle = Drawing.new("Circle")
FOVCircle.Thickness = 2
FOVCircle.Color = Color3.fromRGB(0, 255, 255)
FOVCircle.Transparency = 0.7

-- LÓGICA DO ESP (HIGHLIGHTS)
RunService.Heartbeat:Connect(function()
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character then
            local highlight = player.Character:FindFirstChild("BhBz_ESP")
            if Settings.ESP then
                if not highlight then
                    highlight = Instance.new("Highlight")
                    highlight.Name = "BhBz_ESP"
                    highlight.Parent = player.Character
                    highlight.FillColor = Color3.fromRGB(0, 255, 255)
                    highlight.OutlineColor = Color3.fromRGB(255, 255, 255)
                end
                highlight.Enabled = true
            else
                if highlight then highlight.Enabled = false end
            end
        end
    end
end)

-- LÓGICA DO AIMBOT
local function GetClosestPlayer()
    local Target = nil
    local MaxDist = Settings.FOV

    for _, v in pairs(Players:GetPlayers()) do
        if v ~= LocalPlayer and v.Character and v.Character:FindFirstChild(Settings.AimPart) then
            local Pos, OnScreen = Camera:WorldToViewportPoint(v.Character[Settings.AimPart].Position)
            if OnScreen then
                local MouseDist = (Vector2.new(Mouse.X, Mouse.Y) - Vector2.new(Pos.X, Pos.Y)).Magnitude
                if MouseDist < MaxDist then
                    Target = v.Character[Settings.AimPart]
                    MaxDist = MouseDist
                end
            end
        end
    end
    return Target
end

RunService.RenderStepped:Connect(function()
    FOVCircle.Radius = Settings.FOV
    FOVCircle.Position = UserInputService:GetMouseLocation()
    FOVCircle.Visible = true

    if Settings.Aimbot and UserInputService:IsMouseButtonPressed(Enum.UserInputType.MouseButton2) then -- Ativa segurando o botão direito
        local Target = GetClosestPlayer()
        if Target then
            -- Suavização para não dar ban imediato
            Camera.CFrame = CFrame.new(Camera.CFrame.Position, Target.Position)
        end
    end
end)
