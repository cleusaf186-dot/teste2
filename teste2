local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

local FOV = 100 -- campo de visão do aimbot
local smoothing = 5 -- quanto maior, mais suave a movimentação do mouse

-- Função para pegar a parte certa do inimigo (Head, UpperTorso, HumanoidRootPart)
local function getTargetPart(character)
    return character:FindFirstChild("Head") or character:FindFirstChild("UpperTorso") or character:FindFirstChild("HumanoidRootPart")
end

-- Verifica se a parte está visível (raycast)
local function isVisible(part)
    local origin = Camera.CFrame.Position
    local direction = (part.Position - origin).Unit * 1000
    local raycastParams = RaycastParams.new()
    raycastParams.FilterDescendantsInstances = {LocalPlayer.Character}
    raycastParams.FilterType = Enum.RaycastFilterType.Blacklist

    local result = workspace:Raycast(origin, direction, raycastParams)
    if result then
        return result.Instance:IsDescendantOf(part.Parent)
    end
    return false
end

-- Função para encontrar o alvo mais próximo no FOV
local function getClosestTarget()
    local closestPlayer = nil
    local closestDistance = math.huge

    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("Humanoid") and player.Character.Humanoid.Health > 0 then
            local part = getTargetPart(player.Character)
            if part and isVisible(part) then
                local screenPos, onScreen = Camera:WorldToViewportPoint(part.Position)
                if onScreen then
                    local mousePos = UserInputService:GetMouseLocation()
                    local distance = (Vector2.new(screenPos.X, screenPos.Y) - mousePos).Magnitude
                    if distance < FOV and distance < closestDistance then
                        closestDistance = distance
                        closestPlayer = player
                    end
                end
            end
        end
    end

    return closestPlayer
end

-- Nova função pra pegar qualquer parte visível do personagem para ESP
local function getAnyPart(character)
    for _, part in pairs(character:GetChildren()) do
        if part:IsA("BasePart") and part.Transparency < 1 and part.CanCollide then
            return part
        end
    end
    return nil
end

-- ESP simples com a nova função
local function createESP(player)
    local espBox = Drawing.new("Square")
    espBox.Visible = false
    espBox.Color = Color3.new(1, 0, 0)
    espBox.Thickness = 2
    espBox.Transparency = 1
    espBox.Filled = false

    RunService.RenderStepped:Connect(function()
        if player.Character then
            local part = getAnyPart(player.Character)
            if part then
                local pos, onScreen = Camera:WorldToViewportPoint(part.Position)
                if onScreen then
                    espBox.Visible = true
                    espBox.Size = Vector2.new(40, 60)
                    espBox.Position = Vector2.new(pos.X - espBox.Size.X / 2, pos.Y - espBox.Size.Y / 2)
                else
                    espBox.Visible = false
                end
            else
                espBox.Visible = false
            end
        else
            espBox.Visible = false
        end
    end)
end

-- Criar ESP para todos os jogadores (menos local)
for _, player in pairs(Players:GetPlayers()) do
    if player ~= LocalPlayer then
        createESP(player)
    end
end

-- Aimbot ativado segurando botão direito
UserInputService.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton2 then
        RunService:BindToRenderStep("Aimbot", Enum.RenderPriority.Camera.Value + 1, function()
            local target = getClosestTarget()
            if target and target.Character then
                local targetPart = getTargetPart(target.Character)
                if targetPart then
                    local screenPos, onScreen = Camera:WorldToViewportPoint(targetPart.Position)
                    if onScreen then
                        local mousePos = UserInputService:GetMouseLocation()
                        local delta = Vector2.new(screenPos.X, screenPos.Y) - mousePos
                        mousemoverel(delta.X / smoothing, delta.Y / smoothing)
                    end
                end
            end
        end)
    end
end)

UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton2 then
        RunService:UnbindFromRenderStep("Aimbot")
    end
end)
