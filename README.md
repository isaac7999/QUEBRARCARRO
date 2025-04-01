local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()
local RunService = game:GetService("RunService")
local StarterGui = game:GetService("StarterGui")

-- Criando a Tool
local Tool = Instance.new("Tool")
Tool.Name = "Teleport Tool"
Tool.RequiresHandle = false -- Remove necessidade de uma parte física

-- Função de teleporte
Tool.Activated:Connect(function()
    local targetPosition = Mouse.Hit.p -- Obtém a posição clicada
    if targetPosition then
        local Character = LocalPlayer.Character
        if Character and Character:FindFirstChild("HumanoidRootPart") then
            Character.HumanoidRootPart.CFrame = CFrame.new(targetPosition)
        end
    end
end)

-- Função para teleporte após segurar a Tool
local holdingTime = 5 -- Tempo que precisa segurar a tool (em segundos)
local holding = false

Tool.Equipped:Connect(function()
    holding = true
    -- Espera 5 segundos segurando a tool
    wait(holdingTime)
    
    if holding then
        local targetPosition = Vector3.new(-280, 38, 375) -- Coordenada fixa para teleporte
        local Character = LocalPlayer.Character
        if Character and Character:FindFirstChild("HumanoidRootPart") then
            Character.HumanoidRootPart.CFrame = CFrame.new(targetPosition)
        end
    end
end)

Tool.Unequipped:Connect(function()
    holding = false -- Cancela o teleporte se a tool for retirada antes do tempo
end)

-- Função para lançar carros
local function launchCar()
    if not Tool.Parent:IsDescendantOf(LocalPlayer.Character) then return end
    local Character = LocalPlayer.Character
    if not Character or not Character:FindFirstChild("HumanoidRootPart") then return end
    
    local HumanoidRootPart = Character.HumanoidRootPart
    for _, v in pairs(workspace:GetChildren()) do
        if v:IsA("Model") and v:FindFirstChild("VehicleSeat") then -- Identifica carros
            local Seat = v.VehicleSeat
            if (Seat.Position - HumanoidRootPart.Position).magnitude < 10 then -- Verifica proximidade
                for _, part in pairs(v:GetDescendants()) do
                    if part:IsA("BasePart") then
                        part.Velocity = Vector3.new(math.random(-100, 100), math.random(50, 100), math.random(-100, 100))
                    end
                end
            end
        end
    end
end

-- Verifica constantemente a proximidade de carros
RunService.Heartbeat:Connect(launchCar)

-- Criando botão para segurar a Tool
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

local Button = Instance.new("TextButton")
Button.Size = UDim2.new(0, 200, 0, 50)
Button.Position = UDim2.new(0.5, -100, 0.8, 0)
Button.Text = "Segurar Tool"
Button.Parent = ScreenGui
Button.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
Button.TextColor3 = Color3.fromRGB(255, 255, 255)
Button.Font = Enum.Font.SourceSansBold
Button.TextSize = 20
Button.BorderSizePixel = 2

-- Função para equipar a primeira Tool na Nil Backpack
Button.MouseButton1Click:Connect(function()
    local Backpack = LocalPlayer:FindFirstChild("Backpack")
    if Backpack then
        for _, tool in ipairs(Backpack:GetChildren()) do
            if tool:IsA("Tool") then
                LocalPlayer.Character.Humanoid:EquipTool(tool)
                break
            end
        end
    end
end)

-- Adiciona a Tool na Nil Backpack
Tool.Parent = LocalPlayer.Backpack
