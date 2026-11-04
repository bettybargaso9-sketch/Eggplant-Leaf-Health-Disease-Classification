"use strict";
// =====================================================
// CONFIGURATION
// =====================================================
const MODEL_URL = "https://teachablemachine.withgoogle.com/models/wtAa1ocbT/";
const MIN_CONFIDENCE = 0.65;
const REJECT_CONFIDENCE = 0.65;
const MAX_FILE_SIZE = 15 * 1024 * 1024;
const $ = (id) => document.getElementById(id);
// =====================================================
// HTML ELEMENTS
// =====================================================
const cameraBtn = $("cameraBtn");
const filesBtn = $("filesBtn");
const fileInput = $("fileInput");
const cameraArea = $("cameraArea");
const cameraVideo = $("cameraVideo");
const captureCanvas = $("captureCanvas");
const captureBtn = $("captureBtn");
const closeCameraBtn = $("closeCameraBtn");
const previewArea = $("previewArea");
const preview = $("preview");
const analyzeBtn = $("analyzeBtn");
const retakeBtn = $("retakeBtn");
const resultCard = $("resultCard");
const statusText = $("status");
const historyCard = $("historyCard");
const historyList = $("historyList");
const clearHistoryBtn = $("clearHistoryBtn");
// =====================================================
// APP VARIABLES
// =====================================================
let cameraStream = null;
let model = null;
let modelLoading = null;
let imageReady = false;
let currentImageURL = null;
let detectionHistory = [];
let lastPredictions = [];
// =====================================================
// DISEASE INFORMATION — Unique & Accurate
// =====================================================
const knowledgeBase = {
    healthy: {
        title: "Healthy Eggplant Leaf",
        about: "The leaf appears healthy, with normal green pigmentation and no prominent disease symptoms.",
        isHealthy: true,
        solutions: [
            "Water at the base of the plant to keep foliage dry.",
            "Ensure 6–8 hours of direct sunlight daily; space plants 45–60 cm apart.",
            "Apply balanced organic fertilizer every 2–3 weeks during active growth.",
            "Inspect undersides of leaves weekly for early signs of pests or spots."
        ],
        prevention: [
            "Water early in the morning so leaves dry before evening.",
            "Space plants properly to allow air circulation.",
            "Remove fallen leaves and debris from around plants regularly."
        ]
    },
    cercospora: {
        title: "Cercospora Leaf Spot",
        about: "Fungal disease causing small, round to irregular spots with gray/brown centers and dark purple borders; spots may merge and cause yellowing.",
        solutions: [
            "Pick and dispose of all spotted leaves in sealed bags — do not compost.",
            "Stop overhead watering; use drip or base irrigation only.",
            "Apply copper-based fungicide at label rates every 7–10 days for 3–4 applications.",
            "Thin plants to improve airflow; remove lower leaves touching the soil."
        ],
        prevention: [
            "Avoid wetting leaves when watering.",
            "Rotate crops — do not plant eggplant in the same bed for at least 2 years.",
            "Clear and discard all plant debris after harvest.",
            "Use disease-free seedlings from reputable sources."
        ]
    },
    mosaic: {
        title: "Possible Mosaic Virus Infection",
        about: "Viral infection causing mottled light and dark green patterns, leaf curling, stunted growth, or distorted fruit; spread by sap-sucking insects.",
        solutions: [
            "Isolate affected plants immediately to prevent spread.",
            "Control aphids and whiteflies using yellow sticky traps and insecticidal soap.",
            "Remove and destroy severely infected plants — no cure is available.",
            "Contact your local agricultural office for confirmation and advice."
        ],
        prevention: [
            "Use certified virus-free seedlings; avoid transplants from unknown sources.",
            "Disinfect hands and tools with soapy water or alcohol between plants.",
            "Remove weeds that host aphids and other virus carriers.",
            "Cover young plants with fine mesh netting to block insect vectors."
        ]
    },
    pest: {
        title: "Insect Pest Damage",
        about: "Chewing damage, holes, skeletonized areas, or visible insects/eggs/larvae found on leaves; common pests include flea beetles, caterpillars, and beetles.",
        solutions: [
            "Check both leaf surfaces — focus on undersides and leaf axils — for eggs and larvae.",
            "Hand-pick and destroy visible insects early in the morning.",
            "Spray with insecticidal soap or neem oil, covering all plant surfaces; repeat every 5–7 days.",
            "Apply approved botanical or synthetic insecticides only if infestation worsens; follow local regulations."
        ],
        prevention: [
            "Examine all new plants thoroughly before placing near existing crops.",
            "Plant marigolds or basil nearby to attract predatory insects like ladybugs and lacewings.",
            "Use lightweight row covers over seedlings until flowering begins.",
            "Remove weeds that provide shelter and food sources for pests."
        ]
    },
    wilt: {
        title: "Possible Bacterial Wilt",
        about: "Leaves and stems wilt rapidly even with moist soil; stems may show brown discoloration inside; caused by soil-borne bacteria spread by beetles.",
        solutions: [
            "Mark affected plants and avoid moving soil or tools between them and healthy plants.",
            "Confirm diagnosis by cutting a stem — if milky sap oozes from the cut, bacterial wilt is likely.",
            "Remove and burn or bag and discard confirmed infected plants.",
            "Do not replant solanaceous crops (eggplant, tomato, pepper) in that spot for at least 3 years."
        ],
        prevention: [
            "Select resistant or tolerant varieties when available.",
            "Practice crop rotation with non-related crops like beans, corn, or leafy greens.",
            "Control flea beetles and cucumber beetles which spread the bacteria.",
            "Ensure good drainage and avoid wounding stems during cultivation."
        ]
    },
    early: {
        title: "Early Blight",
        about: "Fungal disease producing brown spots with concentric target-like rings, surrounded by yellow halos; starts on lower leaves and moves upward.",
        solutions: [
            "Prune off all visibly affected leaves and remove from the garden.",
            "Maintain at least 60 cm spacing between plants for maximum airflow.",
            "Apply fungicide containing mancozeb or copper at first sign; repeat every 7–10 days.",
            "Avoid working in plants when foliage is wet to prevent spore spread."
        ],
        prevention: [
            "Water at ground level; use soaker hoses or drip systems.",
            "Remove crop residues immediately after harvest.",
            "Mulch around plants to prevent soil-borne spores splashing onto lower leaves.",
            "Rotate crops with non-solanaceous plants for 2+ years."
        ]
    },
    late: {
        title: "Possible Late Blight",
        about: "Fast-spreading disease causing large, dark brown to black lesions; white fuzzy growth may appear on leaf undersides in humid weather.",
        solutions: [
            "Isolate affected plants immediately — late blight spreads very quickly in moist conditions.",
            "Do not move through wet foliage; spores spread easily on clothing and tools.",
            "Apply protectant fungicide preventively during rainy or humid periods.",
            "Seek immediate advice from local agricultural extension — late blight is a regulated pest in many areas."
        ],
        prevention: [
            "Monitor plants closely during cool, wet, or humid weather.",
            "Space plants widely and prune dense growth to keep the canopy open and dry.",
            "Water early so leaves dry completely by mid-afternoon.",
            "Remove and destroy volunteer plants and weeds that may host the pathogen."
        ]
    },
    mold: {
        title: "Leaf Mold",
        about: "Yellowish irregular patches on upper leaf surfaces; velvety olive-green to brown fungal growth appears directly beneath on the undersides.",
        solutions: [
            "Improve ventilation immediately — open sides of greenhouses or space plants wider.",
            "Remove severely affected lower leaves to increase air movement.",
            "Reduce humidity around plants; avoid misting and overcrowding.",
            "Apply fungicide containing copper or potassium bicarbonate at first detection."
        ],
        prevention: [
            "Maintain good plant spacing and prune dense growth.",
            "Water at the base; never water from above in the late afternoon or evening.",
            "Remove and compost or discard all plant debris at the end of the season.",
            "Choose resistant varieties if available in your area."
        ]
    },
    powdery: {
        title: "Powdery Mildew",
        about: "Distinct white to gray powdery coating on leaf surfaces, stems, and fruit; leaves may yellow, curl, or drop prematurely.",
        solutions: [
            "Remove heavily coated leaves to slow spread.",
            "Apply solution of 1 part milk to 9 parts water weekly as a preventive spray.",
            "Use sulfur-based or potassium bicarbonate fungicides — ensure full coverage.",
            "Avoid excessive nitrogen fertilizer which promotes susceptible new growth."
        ],
        prevention: [
            "Space plants to allow full sun exposure and air movement.",
            "Check plants weekly — powdery mildew is easier to control early.",
            "Use balanced fertilizer and avoid overfeeding.",
            "Clean up fallen leaves and debris where spores overwinter."
        ]
    },
    curl: {
        title: "Leaf Curling / Distortion",
        about: "Leaves curl upward, downward, or become crinkled; may be caused by aphids, whiteflies, viruses, heat stress, water imbalance, or chemical exposure.",
        solutions: [
            "Examine undersides for tiny insects, eggs, or sticky honeydew residue.",
            "Check soil moisture — keep evenly moist but never soggy; mulch to retain water.",
            "Shade plants during extreme heat; water early in the morning.",
            "If curling persists and spreads, isolate the plant and seek expert diagnosis."
        ],
        prevention: [
            "Use healthy, vigorous seedlings free of distortion.",
            "Monitor for insect pests weekly and treat early.",
            "Maintain consistent watering schedule — avoid extreme dryness then flooding.",
            "Apply herbicides far away from vegetable gardens; avoid spraying on windy days."
        ]
    },
    yellow: {
        title: "Leaf Yellowing / Chlorosis",
        about: "Uniform or patchy yellowing may indicate nitrogen deficiency, overwatering/root suffocation, root damage, nematodes, or early disease.",
        solutions: [
            "Check soil — if soggy, reduce watering and improve drainage.",
            "Inspect roots — healthy roots are white/tan; brown/mushy roots indicate rot.",
            "Apply diluted nitrogen-rich fertilizer or foliar feed after confirming deficiency.",
            "If yellowing spreads despite care, send a sample to a diagnostic lab."
        ],
        prevention: [
            "Water deeply but less frequently — let the top 2–3 cm dry between waterings.",
            "Ensure raised beds or good drainage to prevent standing water.",
            "Test soil every 1–2 years and amend based on results.",
            "Check leaves weekly to catch changes early."
        ]
    },
    default: {
        title: "General Leaf Condition",
        about: "The leaf shows signs of stress or damage not clearly matching known conditions.",
        solutions: [
            "Examine both leaf surfaces closely with good lighting.",
            "Note patterns — spots along veins, edges, or random distribution.",
            "Take clear photos and show to a local agricultural expert for confirmation.",
            "Isolate affected plants until cause is identified."
        ],
        prevention: [
            "Maintain proper spacing and airflow.",
            "Water at the base and in the morning.",
            "Clean tools between plants with soapy water.",
            "Monitor plants regularly to catch problems early."
        ]
    }
};
// =====================================================
// STATUS NOTIFICATIONS
// =====================================================
function setStatus(message, type = "normal") {
    statusText.textContent = message;
    statusText.classList.remove("status-error", "status-success");
    statusText.setAttribute("role", "status");
    if (type === "error") {
        statusText.classList.add("status-error");
        statusText.setAttribute("role", "alert");
    } else if (type === "success") {
        statusText.classList.add("status-success");
    }
}
function showInvalidImageError() {
    resultCard.hidden = true;
    setStatus("❌ Error: Not an Eggplant Leaf. Please upload or capture a clear eggplant leaf photo.", "error");
}
// =====================================================
// CLASSIFICATION HELPERS
// =====================================================
function normalizeLabel(className) {
    return String(className || "")
        .toLowerCase()
        .replace(/[_-]+/g, " ")
        .replace(/\s+/g, " ")
        .trim();
}
function isNonEggplantPrediction(className) {
    const label = normalizeLabel(className);
    const rejectedLabels = [
        "not eggplant", "not eggplant leaf", "non eggplant", "non eggplant leaf",
        "not an eggplant", "not an eggplant leaf", "not a eggplant leaf",
        "not a leaf", "not plant", "non plant", "unknown", "background",
        "unrelated", "other", "not a plant", "flower", "fruit", "stem",
        "soil", "hand", "person"
    ];
    return rejectedLabels.some(keyword => label.includes(keyword));
}
function getDiseaseInfo(className) {
    const label = normalizeLabel(className);
    
    if (isNonEggplantPrediction(label)) {
        return { key: "invalid", title: "Not an Eggplant Leaf", isHealthy: false };
    }
    if (label.includes("healthy")) {
        return { ...knowledgeBase.healthy, key: "healthy" };
    }
    const mappings = [
        { words: ["cercospora"], key: "cercospora" },
        { words: ["mosaic", "virus"], key: "mosaic" },
        { words: ["flea beetle", "beetle", "insect", "pest", "damage"], key: "pest" },
        { words: ["bacterial wilt", "wilt"], key: "wilt" },
        { words: ["early blight"], key: "early" },
        { words: ["late blight"], key: "late" },
        { words: ["leaf mold", "leaf mould"], key: "mold" },
        { words: ["powdery mildew"], key: "powdery" },
        { words: ["leaf curl"], key: "curl" },
        { words: ["yellow leaf", "yellowing", "yellow"], key: "yellow" }
    ];
    for (const mapping of mappings) {
        if (mapping.words.some(word => label.includes(word))) {
            return { ...knowledgeBase[mapping.key], key: mapping.key };
        }
    }
    return {
        ...knowledgeBase.default,
        key: "default",
        title: String(className).replace(/[_-]+/g, " ").trim() || knowledgeBase.default.title
    };
}
// =====================================================
// EVENT LISTENERS
// =====================================================
cameraBtn.addEventListener("click", openCamera);
filesBtn.addEventListener("click", openFilePicker);
fileInput.addEventListener("change", handleImage);
captureBtn.addEventListener("click", capturePhoto);
closeCameraBtn.addEventListener("click", closeCamera);
analyzeBtn.addEventListener("click", analyzeImage);
retakeBtn.addEventListener("click", resetApp);
$("resetBtn").addEventListener("click", resetApp);
clearHistoryBtn.addEventListener("click", clearHistory);
function openFilePicker() {
    fileInput.value = "";
    fileInput.click();
}
// =====================================================
// CAMERA — FIXED: No Mirroring
// =====================================================
async function openCamera() {
    if (!navigator.mediaDevices?.getUserMedia) {
        setStatus("Camera access requires HTTPS or localhost. You can upload an image instead.", "error");
        return;
    }
    stopCameraStream();
    cameraBtn.disabled = true;
    setStatus("Requesting camera permission...");
    try {
        let stream;
        try {
            stream = await navigator.mediaDevices.getUserMedia({
                video: { facingMode: "environment", width: { ideal: 1280 }, height: { ideal: 720 } },
                audio: false
            });
        } catch {
            stream = await navigator.mediaDevices.getUserMedia({
                video: { facingMode: "environment" },
                audio: false
            });
        }
        cameraStream = stream;
        cameraVideo.srcObject = stream;
        cameraArea.hidden = false;
        previewArea.hidden = true;
        resultCard.hidden = true;
        await new Promise((resolve, reject) => {
            if (cameraVideo.readyState >= 1) { resolve(); return; }
            cameraVideo.onloadedmetadata = resolve;
            cameraVideo.onerror = reject;
        });
        await cameraVideo.play();
        setStatus("Camera ready. Position the eggplant leaf and capture a photo.");
    } catch (error) {
        console.error("Camera error:", error);
        stopCameraStream();
        cameraArea.hidden = true;
        setStatus("Unable to open camera. Check browser permissions or use Choose from Files.", "error");
    } finally {
        cameraBtn.disabled = false;
    }
}
function capturePhoto() {
    if (!cameraStream || cameraVideo.readyState < 2) {
        setStatus("Please wait for the camera video to load.", "error");
        return;
    }
    captureCanvas.width = cameraVideo.videoWidth || 640;
    captureCanvas.height = cameraVideo.videoHeight || 480;
    const context = captureCanvas.getContext("2d");
    if (!context) {
        setStatus("Unable to access the photo canvas.", "error");
        return;
    }
    // FIXED: Draw without flipping — what you see is what you get
    context.drawImage(cameraVideo, 0, 0, captureCanvas.width, captureCanvas.height);
    captureCanvas.toBlob(blob => {
        if (!blob) {
            setStatus("Unable to capture photo. Please try again.", "error");
            return;
        }
        stopCameraStream();
        cameraArea.hidden = true;
        displayImage(blob);
    }, "image/jpeg", 0.92);
}
function stopCameraStream() {
    if (cameraStream) {
        cameraStream.getTracks().forEach(track => track.stop());
        cameraStream = null;
    }
    cameraVideo.pause();
    cameraVideo.srcObject = null;
}
function closeCamera() {
    stopCameraStream();
    cameraArea.hidden = true;
    setStatus(imageReady ? "Camera closed. Your selected image is ready." : "Select Open Camera or Choose from Files to begin.");
}
// =====================================================
// IMAGE UPLOAD AND PREVIEW
// =====================================================
function displayImage(blobOrFile) {
    if (currentImageURL) {
        URL.revokeObjectURL(currentImageURL);
    }
    imageReady = false;
    lastPredictions = [];
    currentImageURL = URL.createObjectURL(blobOrFile);
    preview.onload = () => {
        imageReady = true;
        previewArea.hidden = false;
        cameraArea.hidden = true;
        resultCard.hidden = true;
        setStatus("Image ready. Press Analyze Leaf to check it.");
    };
    preview.onerror = () => {
        imageReady = false;
        previewArea.hidden = true;
        setStatus("Unable to display this image. Please choose another file.", "error");
    };
    preview.src = currentImageURL;
}
function handleImage(event) {
    const file = event.target.files?.[0];
    if (!file) return;
    if (!file.type.startsWith("image/")) {
        setStatus("Invalid file. Please select an image.", "error");
        fileInput.value = "";
        return;
    }
    if (file.size > MAX_FILE_SIZE) {
        setStatus("Image is too large. Choose an image smaller than 15 MB.", "error");
        fileInput.value = "";
        return;
    }
    stopCameraStream();
    cameraArea.hidden = true;
    displayImage(file);
}
// =====================================================
// LOAD TEACHABLE MACHINE MODEL
// =====================================================
async function loadModel() {
    if (model) return model;
    if (modelLoading) return modelLoading;
    if (typeof tmImage === "undefined") {
        throw new Error("Teachable Machine image library did not load. Check your HTML script tags.");
    }
    const baseURL = MODEL_URL.endsWith("/") ? MODEL_URL : MODEL_URL + "/";
    modelLoading = tmImage.load(baseURL + "model.json", baseURL + "metadata.json");
    try {
        model = await modelLoading;
        return model;
    } finally {
        modelLoading = null;
    }
}
// =====================================================
// ANALYZE IMAGE
// =====================================================
async function analyzeImage() {
    if (!imageReady || !preview.complete || !preview.naturalWidth) {
        setStatus("Please capture or select an image first.", "error");
        return;
    }
    analyzeBtn.disabled = true;
    analyzeBtn.innerHTML = '<i class="fa-solid fa-spinner fa-spin"></i> Checking Image...';
    resultCard.hidden = true;
    setStatus("Loading AI model and checking the image...");
    try {
        const trainedModel = await loadModel();
        const predictions = await trainedModel.predict(preview);
        if (!Array.isArray(predictions) || predictions.length === 0) {
            throw new Error("The model returned no predictions.");
        }
        const validPredictions = predictions
            .filter(item =>
                typeof item.className === "string" &&
                Number.isFinite(item.probability)
            )
            .sort((a, b) => b.probability - a.probability);
        if (validPredictions.length === 0) {
            throw new Error("The model returned invalid predictions.");
        }
        const best = validPredictions[0];
        if (isNonEggplantPrediction(best.className)) {
            lastPredictions = [];
            showInvalidImageError();
            return;
        }
        const nonEggplantPred = validPredictions.find(p => isNonEggplantPrediction(p.className));
        if (nonEggplantPred && nonEggplantPred.probability >= REJECT_CONFIDENCE) {
            lastPredictions = [];
            showInvalidImageError();
            return;
        }
        if (best.probability < MIN_CONFIDENCE) {
            lastPredictions = [];
            setStatus("⚠️ Image not recognized confidently. Please use a clear, well-lit photo showing only an eggplant leaf.", "error");
            resultCard.hidden = true;
            return;
        }
        const info = getDiseaseInfo(best.className);
        if (info.key === "invalid") {
            lastPredictions = [];
            showInvalidImageError();
            return;
        }
        lastPredictions = validPredictions;
        showResult(best.className, best.probability, true, validPredictions);
    } catch (error) {
        console.error("Analysis error:", error);
        resultCard.hidden = true;
        setStatus("Analysis failed. Check your internet connection, model URL, and model.json/metadata.json files.", "error");
    } finally {
        analyzeBtn.disabled = false;
        analyzeBtn.innerHTML = '<i class="fa-solid fa-magnifying-glass"></i> Analyze Leaf';
    }
}
// =====================================================
// DISPLAY RESULT — FIXED: Unique Content Per Condition
// =====================================================
function createListItems(listElement, items) {
    listElement.replaceChildren();
    for (const item of items) {
        const li = document.createElement("li");
        li.textContent = item;
        listElement.appendChild(li);
    }
}
function showResult(className, probability, addToHistoryFlag = true, allPredictions = lastPredictions) {
    const info = getDiseaseInfo(className);
    if (info.key === "invalid") {
        showInvalidImageError();
        return;
    }
    const confidence = Math.round(Math.max(0, Math.min(1, probability)) * 100);
    const resultTitle = $("resultTitle");
    const resultIcon = $("resultIcon");
    const resultDescription = $("resultDescription");
    const diseaseInfoContainer = $("diseaseInfoContainer");
    const diseaseAboutText = $("diseaseAboutText");
    const solutionsHeader = $("solutionsHeader");
    const diseaseSolutionsList = $("diseaseSolutionsList");
    const diseasePreventionList = $("diseasePreventionList");
    
    // Populate — every condition shows ITS OWN content
    resultTitle.textContent = info.title;
    diseaseAboutText.textContent = info.about;
    
    if (info.isHealthy) {
        resultTitle.className = "title-healthy";
        resultIcon.textContent = "✅";
        resultDescription.textContent = `The model predicts a healthy eggplant leaf (${confidence}% confidence).`;
        solutionsHeader.innerHTML = '<i class="fa-solid fa-seedling icon-green"></i> Plant Care Tips';
    } else {
        resultTitle.className = "title-diseased";
        resultIcon.textContent = "⚠️";
        resultDescription.textContent = `Predicted condition: ${info.title} (${confidence}% confidence). This is an AI prediction, not a confirmed diagnosis.`;
        solutionsHeader.innerHTML = '<i class="fa-solid fa-kit-medical icon-green"></i> Recommended Actions';
    }
    
    // These are now UNIQUE per condition — no repetition
    createListItems(diseaseSolutionsList, info.solutions);
    createListItems(diseasePreventionList, info.prevention);
    
    $("confidenceBar").style.width = confidence + "%";
    $("confidenceText").textContent = confidence + "%";
    diseaseInfoContainer.hidden = false;
    resultCard.hidden = false;
    setStatus("Analysis completed.", "success");
    resultCard.scrollIntoView({ behavior: "smooth", block: "start" });
    
    if (addToHistoryFlag) {
        addToHistory(info.title, confidence, className, probability, allPredictions);
    }
}
// =====================================================
// DETECTION HISTORY
// =====================================================
function addToHistory(title, confidence, className, probability, predictions = []) {
    detectionHistory.unshift({
        id: Date.now() + Math.random(),
        title, confidence, className, probability,
        predictions: predictions.map(item => ({
            className: item.className,
            probability: item.probability
        })),
        time: new Date().toLocaleString([], { dateStyle: "short", timeStyle: "short" })
    });
    renderHistory();
}
function renderHistory() {
    historyList.replaceChildren();
    if (detectionHistory.length === 0) {
        historyCard.hidden = true;
        return;
    }
    historyCard.hidden = false;
    detectionHistory.forEach(item => {
        const li = document.createElement("li");
        li.className = "history-item";
        const details = document.createElement("div");
        details.className = "history-details";
        const title = document.createElement("strong");
        title.textContent = item.title;
        const badge = document.createElement("span");
        badge.className = "history-badge " + (item.title.toLowerCase().includes("healthy") ? "badge-healthy" : "badge-diseased");
        badge.textContent = item.confidence + "%";
        details.append(title, badge);
        const time = document.createElement("span");
        time.className = "history-time";
        time.textContent = item.time;
        const actions = document.createElement("div");
        actions.className = "history-actions";
        const restoreBtn = document.createElement("button");
        restoreBtn.type = "button";
        restoreBtn.className = "history-action-btn restore-btn";
        restoreBtn.title = "Restore this result";
        restoreBtn.innerHTML = '<i class="fa-solid fa-eye"></i>';
        restoreBtn.addEventListener("click", e => {
            e.stopPropagation();
            lastPredictions = item.predictions || [];
            showResult(item.className, item.probability, false, lastPredictions);
        });
        const deleteBtn = document.createElement("button");
        deleteBtn.type = "button";
        deleteBtn.className = "history-action-btn delete-btn";
        deleteBtn.title = "Delete this history entry";
        deleteBtn.innerHTML = '<i class="fa-solid fa-trash"></i>';
        deleteBtn.addEventListener("click", e => {
            e.stopPropagation();
            detectionHistory = detectionHistory.filter(entry => entry.id !== item.id);
            renderHistory();
        });
        actions.append(restoreBtn, deleteBtn);
        li.append(details, time, actions);
        li.addEventListener("click", () => {
            lastPredictions = item.predictions || [];
            showResult(item.className, item.probability, false, lastPredictions);
        });
        historyList.appendChild(li);
    });
}
function clearHistory() {
    if (detectionHistory.length === 0) return;
    if (!window.confirm("Are you sure you want to clear all detection history?")) return;
    detectionHistory = [];
    renderHistory();
    setStatus("Detection history cleared.", "success");
}
// =====================================================
// RESET AND CLEANUP
// =====================================================
function resetApp() {
    stopCameraStream();
    if (currentImageURL) {
        URL.revokeObjectURL(currentImageURL);
        currentImageURL = null;
    }
    fileInput.value = "";
    preview.removeAttribute("src");
    imageReady = false;
    lastPredictions = [];
    previewArea.hidden = true;
    cameraArea.hidden = true;
    resultCard.hidden = true;
    analyzeBtn.disabled = false;
    analyzeBtn.innerHTML = '<i class="fa-solid fa-magnifying-glass"></i> Analyze Leaf';
    setStatus("Select Open Camera or Choose from Files to begin.");
}
window.addEventListener("pagehide", stopCameraStream);
