/**
 * ゲームのメインロジック
 */

// --- Canvas & Context Setup ---
const canvas = document.getElementById('gameCanvas');
const ctx = canvas.getContext('2d');
const mCanvas = document.getElementById('minimapCanvas');
const mCtx = mCanvas.getContext('2d');

function resizeCanvas() {
    canvas.width = window.innerWidth;
    canvas.height = window.innerHeight;
}
window.addEventListener('resize', resizeCanvas);
resizeCanvas();

// --- 定数・ゲーム設定 ---
const MAP_W = 600; // マップの幅 (タイル数) 円形ワールド化に伴い拡張
const MAP_H = 600; // マップの高さ (タイル数) 円形ワールド化に伴い拡張
const WORLD_RADIUS = 290; // 最初の石像を中心とした円形マップの半径 (タイル数)
const TS = 48;     // タイルサイズ (px)

const TILE = {
    VOID: 0,  // 円形マップ外の虚無（通行不可・破壊不可）
    DIRT: 1,  // 土
    ROCK: 2,  // 岩石
    IRON: 3,  // 鉄鉱石
    BOMB: 4,  // 爆弾岩（見た目は土）
    HAY: 5,   // 干し草の床
    FLOOR: 6, // 掘った後の地面
    BOMB_ACTIVE: 7, // 起爆して点滅中の爆弾岩
    DEEP_DIRT: 8, // 深層の土（強化つるはしが必要）
    MAZE_BRICK: 9, // メイズアンレンガ（破壊不能）
    ROOT: 10, // 地下茎
    WORKBENCH: 11, // 木の作業台
    MAZE_UNREAL: 12, // 装置
    MAZE_BLOCK: 13,   // 生成されるブロック
    WATER: 14, // 水（渡れない・破壊不可）
    CHASM: 15, // 切れ目（渡れない・破壊不可）
    SWAMP_WATER: 16, // 湿地の水
    MUD: 17, // 泥
    ASH: 18, // 灰（壁ブロック・1発破壊）
    ASH_FLOOR: 19, // 灰色の地面（通行可能）
    WITHERED_TREE: 20, // 廃離の木
    WORKBENCH_ROCK: 21, // 石の作業台
    WORKBENCH_SWAMP: 22, // 沼地の作業台
    WORKBENCH_WITHERED: 23, // 廃離の作業台
    DIRT_CHEST: 24, // 土のチェスト
    WOOD_CHEST: 25, // 木のチェスト
    ROCK_CHEST: 26,
    IRON_CHEST: 27,
    MUD_CHEST: 28,
    ASH_CHEST: 29,
    WITHERED_CHEST: 30,
    MAZE_CHEST: 31,
    GEERA_CHEST: 32, // ギーラチェスト
    MEDUSA_CHEST: 33, // メデューサチェスト
    ANVIL: 34, // 金床
　FOREST_FLOOR: 35, // 森林の緑の地面（通行可能・模様）
    FOREST_TREE: 36,  // 森林の巨大な木（幹の根元・伐採可能）
};

// 高精細SVGアイテムアイコン生成関数
function getItemSvg(id) {
    if (!id) return '';
    const tierColors = {
        junk: { main: '#94a3b8', dark: '#475569', light: '#f8fafc', handle: '#334155', gem: null },
        dirt: { main: '#8d6e63', dark: '#4e342e', light: '#d7ccc8', handle: '#3e2723', gem: null },
        wood: { main: '#d97706', dark: '#78350f', light: '#fde68a', handle: '#451a03', gem: null },
        rock: { main: '#64748b', dark: '#334155', light: '#e2e8f0', handle: '#1e293b', gem: null },
        iron: { main: '#cbd5e1', dark: '#475569', light: '#ffffff', handle: '#0f172a', gem: '#38bdf8' },
        maze: { main: '#a855f7', dark: '#581c87', light: '#f3e8ff', handle: '#2e1065', gem: '#f43f5e' }
    };

    if (id.startsWith('sword_')) {
        let t = id.replace('sword_', '');
        let c = tierColors[t] || tierColors.junk;
        if (t === 'mud') c = { main: '#5d4037', dark: '#3e2723', light: '#a1887f', handle: '#261611', gem: null };
        if (t === 'ash') c = { main: '#71717a', dark: '#3f3f46', light: '#e4e4e7', handle: '#27272a', gem: null };
        if (t === 'withered') c = { main: '#475569', dark: '#1e293b', light: '#94a3b8', handle: '#0f172a', gem: null };
        if (t === 'medusa') c = { main: '#10b981', dark: '#065f46', light: '#a7f3d0', handle: '#022c22', gem: '#facc15' };
        
        const gem = c.gem ? `<circle cx="16" cy="22.5" r="1.8" fill="${c.gem}" stroke="#000" stroke-width="0.5"/>` : '';
        return `<svg viewBox="0 0 32 32" class="w-8 h-8 drop-shadow-md">
            <path d="M16 2 L22 8 L18 19 L16 19 Z" fill="${c.main}" />
            <path d="M16 2 L16 19 L14 19 L10 8 Z" fill="${c.light}" />
            <line x1="16" y1="2" x2="16" y2="19" stroke="${c.dark}" stroke-width="1" opacity="0.6"/>
            <rect x="9" y="18" width="14" height="3" rx="1.5" fill="#b45309" stroke="#78350f" stroke-width="1"/>
            <rect x="12" y="17" width="8" height="5" rx="1" fill="#f59e0b" stroke="#92400e" stroke-width="1"/>
            <rect x="13.5" y="21" width="5" height="6" fill="${c.handle}"/>
            <line x1="13.5" y1="23" x2="18.5" y2="23" stroke="#000" stroke-width="1" opacity="0.4"/>
            <line x1="13.5" y1="25" x2="18.5" y2="25" stroke="#000" stroke-width="1" opacity="0.4"/>
            <circle cx="16" cy="28.5" r="2.5" fill="#f59e0b" stroke="#92400e" stroke-width="1"/>
            ${gem}
        </svg>`;
    }

    if (id.startsWith('pickaxe_')) {
        let t = id.replace('pickaxe_', '');
        let c = tierColors[t] || tierColors.junk;
        if (t === 'mud') c = { main: '#5d4037', dark: '#3e2723', light: '#a1887f', handle: '#261611', gem: null };
        if (t === 'ash') c = { main: '#71717a', dark: '#3f3f46', light: '#e4e4e7', handle: '#27272a', gem: null };
        if (t === 'withered') c = { main: '#475569', dark: '#1e293b', light: '#94a3b8', handle: '#0f172a', gem: null };
        if (t === 'medusa') c = { main: '#10b981', dark: '#065f46', light: '#a7f3d0', handle: '#022c22', gem: '#facc15' };
        
        const gem = c.gem ? `<polygon points="16,6 18,9 16,12 14,9" fill="${c.gem}" stroke="#000" stroke-width="0.5"/>` : '';
        return `<svg viewBox="0 0 32 32" class="w-8 h-8 drop-shadow-md">
            <rect x="14" y="8" width="4" height="22" rx="1.5" fill="#78350f" stroke="#451a03" stroke-width="1"/>
            <line x1="14" y1="14" x2="18" y2="14" stroke="#451a03" stroke-width="1"/>
            <line x1="14" y1="20" x2="18" y2="20" stroke="#451a03" stroke-width="1"/>
            <line x1="14" y1="26" x2="18" y2="26" stroke="#451a03" stroke-width="1"/>
            <path d="M4 12 Q16 2 28 12 L25 15 Q16 7 7 15 Z" fill="${c.main}" stroke="${c.dark}" stroke-width="1.5" stroke-linejoin="round"/>
            <path d="M5 11 Q16 3 27 11 L25 13 Q16 6 7 13 Z" fill="${c.light}" opacity="0.8"/>
            <rect x="13" y="6" width="6" height="5" rx="1" fill="#475569" stroke="#1e293b" stroke-width="1"/>
            ${gem}
        </svg>`;
    }

    if (id.startsWith('helmet_')) {
        let t = id.replace('helmet_', '');
        let c = tierColors[t] || tierColors.junk;
        if (t === 'mud') c = { main: '#5d4037', dark: '#3e2723', light: '#a1887f' };
        if (t === 'ash') c = { main: '#71717a', dark: '#3f3f46', light: '#e4e4e7' };
        if (t === 'withered') c = { main: '#475569', dark: '#1e293b', light: '#94a3b8' };
        if (t === 'medusa') c = { main: '#10b981', dark: '#065f46', light: '#a7f3d0' };
        return `<svg viewBox="0 0 32 32" class="w-8 h-8 drop-shadow-md">
            <path d="M6 18 Q6 4 16 4 Q26 4 26 18 L26 24 L22 24 L22 18 L10 18 L10 24 L6 24 Z" fill="${c.main}" stroke="${c.dark}" stroke-width="2"/>
            <path d="M8 16 Q8 6 16 6 Q24 6 24 16" fill="none" stroke="${c.light}" stroke-width="2"/>
            <rect x="14" y="6" width="4" height="10" fill="${c.dark}"/>
        </svg>`;
    }

    if (id.startsWith('chestplate_')) {
        let t = id.replace('chestplate_', '');
        let c = tierColors[t] || tierColors.junk;
        if (t === 'mud') c = { main: '#5d4037', dark: '#3e2723', light: '#a1887f' };
        if (t === 'ash') c = { main: '#71717a', dark: '#3f3f46', light: '#e4e4e7' };
        if (t === 'withered') c = { main: '#475569', dark: '#1e293b', light: '#94a3b8' };
        if (t === 'medusa') c = { main: '#10b981', dark: '#065f46', light: '#a7f3d0' };
        return `<svg viewBox="0 0 32 32" class="w-8 h-8 drop-shadow-md">
            <path d="M8 6 L24 6 L28 12 L24 14 L24 28 L8 28 L8 14 L4 12 Z" fill="${c.main}" stroke="${c.dark}" stroke-width="2" stroke-linejoin="round"/>
            <path d="M12 6 L12 10 L20 10 L20 6" fill="none" stroke="${c.dark}" stroke-width="2"/>
            <line x1="16" y1="12" x2="16" y2="26" stroke="${c.dark}" stroke-width="2"/>
            <path d="M10 12 L14 14 L14 26" fill="none" stroke="${c.light}" stroke-width="1.5" opacity="0.6"/>
        </svg>`;
    }

    if (id.startsWith('boots_')) {
        let t = id.replace('boots_', '');
        let c = tierColors[t] || tierColors.junk;
        if (t === 'mud') c = { main: '#5d4037', dark: '#3e2723', light: '#a1887f' };
        if (t === 'ash') c = { main: '#71717a', dark: '#3f3f46', light: '#e4e4e7' };
        if (t === 'withered') c = { main: '#475569', dark: '#1e293b', light: '#94a3b8' };
        if (t === 'medusa') c = { main: '#10b981', dark: '#065f46', light: '#a7f3d0' };
        return `<svg viewBox="0 0 32 32" class="w-8 h-8 drop-shadow-md">
            <path d="M8 12 L14 12 L14 24 L6 24 L6 18 Z" fill="${c.main}" stroke="${c.dark}" stroke-width="2" stroke-linejoin="round"/>
            <path d="M18 12 L24 12 L26 18 L26 24 L18 24 Z" fill="${c.main}" stroke="${c.dark}" stroke-width="2" stroke-linejoin="round"/>
            <line x1="8" y1="22" x2="14" y2="22" stroke="${c.dark}" stroke-width="2"/>
            <line x1="18" y1="22" x2="24" y2="22" stroke="${c.dark}" stroke-width="2"/>
        </svg>`;
    }

    if (id.startsWith('shield_') && id !== 'shield_junk') {
        let t = id.replace('shield_', '');
        let c = tierColors[t] || tierColors.junk;
        if (t === 'mud') c = { main: '#5d4037', dark: '#3e2723', light: '#a1887f', gem: null };
        if (t === 'ash') c = { main: '#71717a', dark: '#3f3f46', light: '#e4e4e7', gem: null };
        if (t === 'withered') c = { main: '#475569', dark: '#1e293b', light: '#94a3b8', gem: null };
        if (t === 'medusa') c = { main: '#10b981', dark: '#065f46', light: '#a7f3d0', gem: null };

        return `<svg viewBox="0 0 32 32" class="w-8 h-8 drop-shadow-md">
            <path d="M5 4 L27 4 L27 16 C27 26 16 30 16 30 C16 30 5 26 5 16 Z" fill="${c.main}" stroke="${c.dark}" stroke-width="2.5" stroke-linejoin="round"/>
            <path d="M7 6 L25 6 L25 15 C25 24 16 27 16 27 C16 27 7 24 7 15 Z" fill="none" stroke="${c.light}" stroke-width="2" opacity="0.6"/>
            <path d="M16 4 L16 30" stroke="${c.dark}" stroke-width="1.5" opacity="0.7"/>
            <path d="M5 12 L27 12 M5 18 L27 18" stroke="${c.dark}" stroke-width="1.5" opacity="0.5"/>
            <circle cx="16" cy="15" r="4" fill="${c.dark}" stroke="${c.light}" stroke-width="1.5"/>
            <circle cx="16" cy="15" r="1.5" fill="${c.light}"/>
        </svg>`;
    }

    if (id.startsWith('workbench')) {
        let main = '#b45309', dark = '#451a03', leg = '#78350f', top = '#d97706', tool = '#94a3b8';
        if (id === 'workbench_rock') { main = '#64748b'; dark = '#1e293b'; leg = '#334155'; top = '#94a3b8'; tool = '#475569'; }
        if (id === 'workbench_swamp') { main = '#2e4033'; dark = '#151e18'; leg = '#1e2b22'; top = '#3e5c43'; tool = '#a9dfbf'; }
        if (id === 'workbench_withered') { main = '#475569'; dark = '#0f172a'; leg = '#1e293b'; top = '#64748b'; tool = '#cbd5e1'; }
    
        return `<svg viewBox="0 0 32 32" class="w-8 h-8 drop-shadow-md">
            <rect x="6" y="14" width="4" height="14" fill="${leg}" stroke="${dark}" stroke-width="1.5"/>
            <rect x="22" y="14" width="4" height="14" fill="${leg}" stroke="${dark}" stroke-width="1.5"/>
            <rect x="4" y="9" width="24" height="7" rx="1" fill="${main}" stroke="${dark}" stroke-width="1.5"/>
            <rect x="4" y="9" width="24" height="3" rx="1" fill="${top}"/>
            <rect x="8" y="19" width="16" height="2" fill="${dark}" opacity="0.5"/>
            <!-- 卓上の工具 -->
            <rect x="7" y="6" width="3" height="4" fill="#f59e0b"/>
            <rect x="9" y="5" width="5" height="4" fill="${tool}"/>
            <rect x="20" y="7" width="5" height="2" fill="${tool}"/>
        </svg>`;
    }

    if (id === 'anvil') {
        return `<svg viewBox="0 0 32 32" class="w-8 h-8 drop-shadow-md">
            <rect x="6" y="24" width="20" height="4" rx="1" fill="#1e293b"/>
            <path d="M12 24 L14 16 L18 16 L20 24 Z" fill="#334155"/>
            <rect x="8" y="10" width="16" height="6" rx="1" fill="#475569"/>
            <path d="M8 10 Q6 10 4 14 L8 14 Z" fill="#475569"/>
            <rect x="24" y="10" width="4" height="6" rx="1" fill="#475569"/>
            <line x1="8" y1="12" x2="26" y2="12" stroke="#94a3b8" stroke-width="2"/>
        </svg>`;
    }

    if (id.endsWith('_chest')) {
        let baseColor, trimColor, lockColor = '#f1c40f';
        if (id === 'dirt_chest') { baseColor = '#6d4c41'; trimColor = '#3e2723'; }
        else if (id === 'wood_chest') { baseColor = '#b45309'; trimColor = '#78350f'; }
        else if (id === 'rock_chest') { baseColor = '#64748b'; trimColor = '#334155'; }
        else if (id === 'iron_chest') { baseColor = '#cbd5e1'; trimColor = '#64748b'; lockColor = '#38bdf8'; }
        else if (id === 'mud_chest') { baseColor = '#5d4037'; trimColor = '#27140e'; lockColor = '#a1887f'; }
        else if (id === 'ash_chest') { baseColor = '#94a3b8'; trimColor = '#475569'; lockColor = '#e2e8f0'; }
        else if (id === 'withered_chest') { baseColor = '#475569'; trimColor = '#1e293b'; lockColor = '#94a3b8'; }
        else if (id === 'maze_chest') { baseColor = '#a855f7'; trimColor = '#4c1d95'; lockColor = '#f43f5e'; }
        else if (id === 'geera_chest') { baseColor = '#d35400'; trimColor = '#873600'; lockColor = '#f39c12'; }
        else if (id === 'medusa_chest') { baseColor = '#117a65'; trimColor = '#0b5345'; lockColor = '#2ecc71'; }
        
        return `<svg viewBox="0 0 32 32" class="w-8 h-8 drop-shadow-md">
            <rect x="4" y="8" width="24" height="18" rx="2" fill="${trimColor}" />
            <rect x="6" y="10" width="20" height="14" fill="${baseColor}" />
            <rect x="14" y="16" width="4" height="4" rx="1" fill="${lockColor}" />
            <line x1="4" y1="14" x2="28" y2="14" stroke="${trimColor}" stroke-width="2"/>
        </svg>`;
    }

    if (id === 'geera_axe') {
        return `<svg viewBox="0 0 32 32" class="w-8 h-8 drop-shadow-md">
            <rect x="14" y="4" width="4" height="26" rx="1.5" fill="#4e342e" stroke="#2e1c15" stroke-width="1"/>
            <path d="M14 8 Q4 4 2 14 Q8 16 14 12 Z" fill="#e67e22" stroke="#d35400" stroke-width="1.5" stroke-linejoin="round"/>
            <path d="M18 8 Q28 4 30 14 Q24 16 18 12 Z" fill="#e67e22" stroke="#d35400" stroke-width="1.5" stroke-linejoin="round"/>
            <path d="M14 6 Q5 3 3 13 L5 13 Q7 6 14 9 Z" fill="#f39c12" opacity="0.7"/>
            <path d="M18 6 Q27 3 29 13 L27 13 Q25 6 18 9 Z" fill="#f39c12" opacity="0.7"/>
            <circle cx="16" cy="11" r="2" fill="#bdc3c7"/>
        </svg>`;
    }
    if (id === 'medusa_eye') {
        return `<svg viewBox="0 0 32 32" class="w-8 h-8 drop-shadow-md">
            <circle cx="16" cy="16" r="10" fill="#117a65" stroke="#0b5345" stroke-width="2"/>
            <circle cx="16" cy="16" r="5" fill="#f1c40f"/>
            <rect x="15" y="12" width="2" height="8" fill="#000" rx="1"/>
        </svg>`;
    }
    if (id === 'geera_plate' || id === 'medusa_plate') {
        let color = id === 'geera_plate' ? '#e67e22' : '#2ecc71';
        return `<svg viewBox="0 0 32 32" class="w-8 h-8 drop-shadow-md">
            <rect x="6" y="6" width="20" height="20" rx="3" fill="#64748b" stroke="#334155" stroke-width="2"/>
            <circle cx="16" cy="16" r="6" fill="${color}"/>
            <path d="M16 10 L22 16 L16 22 L10 16 Z" fill="#fff" opacity="0.3"/>
        </svg>`;
    }

    const icons = {
        maze_pickaxe: `<svg viewBox="0 0 32 32" class="w-8 h-8 drop-shadow-lg">
            <rect x="14" y="8" width="4" height="23" rx="1.5" fill="#4c1d95" stroke="#2e1065" stroke-width="1.5"/>
            <path d="M3 13 Q16 1 29 13 L26 17 Q16 7 6 17 Z" fill="#d946ef" stroke="#701a75" stroke-width="2" stroke-linejoin="round"/>
            <path d="M5 12 Q16 3 27 12 L25 14 Q16 6 7 14 Z" fill="#fdf4ff" opacity="0.7"/>
            <polygon points="16,2 20,9 16,16 12,9" fill="#0ea5e9" stroke="#fff" stroke-width="1.5"/>
            <circle cx="16" cy="9" r="2" fill="#fff"/>
        </svg>`,
        maze_shield: `<svg viewBox="0 0 32 32" class="w-8 h-8 drop-shadow-lg">
            <path d="M6 4 L26 4 L26 16 C26 25 16 30 16 30 C16 30 6 25 6 16 Z" fill="#312e81" stroke="#c084fc" stroke-width="2.5" stroke-linejoin="round"/>
            <path d="M16 4 L16 30 M8 13 L24 13 M8 20 L24 20" stroke="#a855f7" stroke-width="2" opacity="0.8"/>
            <circle cx="16" cy="16" r="4.5" fill="#c026d3" stroke="#fdf4ff" stroke-width="1.5"/>
            <circle cx="16" cy="16" r="1.5" fill="#fff"/>
        </svg>`,
        maze_ingot: `<svg viewBox="0 0 32 32" class="w-8 h-8 drop-shadow-md">
            <polygon points="5,18 16,9 27,18 16,27" fill="#a855f7" stroke="#4c1d95" stroke-width="2" stroke-linejoin="round"/>
            <polygon points="5,18 16,9 16,27" fill="#d8b4fe"/>
            <polygon points="16,9 27,18 27,20 16,29 5,20" fill="#7e22ce" opacity="0.7"/>
            <line x1="16" y1="9" x2="16" y2="27" stroke="#f3e8ff" stroke-width="1.5"/>
        </svg>`,
        compass: `<svg viewBox="0 0 32 32" class="w-8 h-8 drop-shadow-md">
            <circle cx="16" cy="16" r="12" fill="#1e293b" stroke="#f59e0b" stroke-width="3"/>
            <circle cx="16" cy="16" r="9" fill="#0f172a" stroke="#475569" stroke-width="1"/>
            <polygon points="16,6 19,16 16,18 13,16" fill="#ef4444"/>
            <polygon points="16,26 19,16 16,14 13,16" fill="#e2e8f0"/>
            <circle cx="16" cy="16" r="2.5" fill="#f59e0b" stroke="#000" stroke-width="1"/>
            <circle cx="16" cy="6" r="1.5" fill="#ef4444"/>
        </svg>`,
        heiza_compass: `<svg viewBox="0 0 32 32" class="w-8 h-8 drop-shadow-md">
            <circle cx="16" cy="16" r="12" fill="#1e293b" stroke="#10b981" stroke-width="3"/>
            <circle cx="16" cy="16" r="9" fill="#064e3b" stroke="#047857" stroke-width="1"/>
            <polygon points="16,6 19,16 16,18 13,16" fill="#facc15"/>
            <polygon points="16,26 19,16 16,14 13,16" fill="#cbd5e1"/>
            <circle cx="16" cy="16" r="2.5" fill="#10b981" stroke="#000" stroke-width="1"/>
            <circle cx="16" cy="6" r="1.5" fill="#facc15"/>
        </svg>`,
        withered_compass: `<svg viewBox="0 0 32 32" class="w-8 h-8 drop-shadow-md">
            <circle cx="16" cy="16" r="12" fill="#1e293b" stroke="#10b981" stroke-width="3"/>
            <circle cx="16" cy="16" r="9" fill="#022c22" stroke="#047857" stroke-width="1"/>
            <polygon points="16,6 19,16 16,18 13,16" fill="#a7f3d0"/>
            <polygon points="16,26 19,16 16,14 13,16" fill="#065f46"/>
            <circle cx="16" cy="16" r="2.5" fill="#10b981" stroke="#000" stroke-width="1"/>
            <circle cx="16" cy="6" r="1.5" fill="#a7f3d0"/>
        </svg>`,
        maze_summon: `<svg viewBox="0 0 32 32" class="w-8 h-8 drop-shadow-md">
            <rect x="8" y="4" width="16" height="24" fill="#fef3c7" stroke="#b45309" stroke-width="2"/>
            <rect x="4" y="2" width="24" height="4" rx="1" fill="#b45309"/>
            <rect x="4" y="26" width="24" height="4" rx="1" fill="#b45309"/>
            <path d="M12 10 L20 10 M12 16 L20 16 M12 22 L16 22" stroke="#d97706" stroke-width="2" stroke-linecap="round"/>
            <circle cx="16" cy="16" r="4" fill="none" stroke="#ef4444" stroke-width="2"/>
            <polygon points="16,13 18,18 14,18" fill="#ef4444"/>
        </svg>`,
        geera_scale: `<svg viewBox="0 0 32 32" class="w-8 h-8 drop-shadow-md">
            <path d="M16 4 Q28 12 16 28 Q4 12 16 4 Z" fill="#ea580c" stroke="#9a3412" stroke-width="2" stroke-linejoin="round"/>
            <path d="M16 4 Q24 12 16 24 Q8 12 16 4 Z" fill="#f97316"/>
            <path d="M16 6 Q20 12 16 20 Q12 12 16 6 Z" fill="#fdba74"/>
        </svg>`,
        medusa_hair: `<svg viewBox="0 0 32 32" class="w-8 h-8 drop-shadow-md">
            <path d="M8 28 Q4 20 10 16 T16 8 T24 16 T20 28" fill="none" stroke="#10b981" stroke-width="4" stroke-linecap="round"/>
            <path d="M14 26 Q10 20 16 12 T26 18 T24 26" fill="none" stroke="#059669" stroke-width="3" stroke-linecap="round"/>
            <circle cx="24" cy="16" r="2.5" fill="#047857"/>
            <circle cx="26" cy="18" r="2.5" fill="#047857"/>
            <circle cx="24" cy="16" r="1" fill="#facc15"/>
            <circle cx="26" cy="18" r="1" fill="#facc15"/>
        </svg>`,
        dirt: `<svg viewBox="0 0 32 32" class="w-8 h-8 drop-shadow-md">
            <rect x="4" y="4" width="24" height="24" rx="3" fill="#6d4c41" stroke="#3e2723" stroke-width="2"/>
            <path d="M4 8 Q16 12 28 6 L28 4 L4 4 Z" fill="#8d6e63"/>
            <path d="M4 28 L28 28 L28 24 Q16 20 4 26 Z" fill="#4e342e"/>
            <circle cx="10" cy="14" r="2" fill="#4e342e"/><circle cx="22" cy="18" r="2.5" fill="#3e2723"/><circle cx="14" cy="22" r="1.5" fill="#a1887f"/>
        </svg>`,
        ash: `<svg viewBox="0 0 32 32" class="w-8 h-8 drop-shadow-md">
            <path d="M4 25 Q10 10 16 12 Q22 8 28 25 Z" fill="#94a3b8" stroke="#334155" stroke-width="2" stroke-linejoin="round"/>
            <path d="M7 25 Q15 14 25 25 Z" fill="#cbd5e1"/>
            <circle cx="12" cy="14" r="1.5" fill="#e2e8f0"/><circle cx="20" cy="12" r="1" fill="#f1f5f9"/>
            <path d="M10 20 Q16 18 22 20" stroke="#64748b" stroke-width="1.5" fill="none"/>
        </svg>`,
        mud: `<svg viewBox="0 0 32 32" class="w-8 h-8 drop-shadow-md">
            <rect x="4" y="4" width="24" height="24" rx="4" fill="#5d4037" stroke="#27140e" stroke-width="2"/>
            <path d="M4 12 Q16 16 28 10 L28 4 L4 4 Z" fill="#795548"/>
            <circle cx="12" cy="16" r="2.5" fill="#3e2723"/><circle cx="20" cy="20" r="3" fill="#27140e"/><circle cx="10" cy="23" r="2" fill="#8d6e63"/>
        </svg>`,
        rock: `<svg viewBox="0 0 32 32" class="w-8 h-8 drop-shadow-md">
            <polygon points="9,5 23,4 29,14 25,26 10,28 4,17" fill="#64748b" stroke="#1e293b" stroke-width="2" stroke-linejoin="round"/>
            <polygon points="9,5 23,4 18,13 11,12" fill="#94a3b8"/>
            <polygon points="23,4 29,14 18,13" fill="#475569"/>
            <polygon points="4,17 11,12 18,13 10,28" fill="#526070"/>
        </svg>`,
        iron: `<svg viewBox="0 0 32 32" class="w-8 h-8 drop-shadow-md">
            <polygon points="5,22 11,9 25,9 28,22" fill="#94a3b8" stroke="#1e293b" stroke-width="2" stroke-linejoin="round"/>
            <polygon points="11,9 25,9 21,15 9,15" fill="#ffffff"/>
            <polygon points="25,9 28,22 21,15" fill="#475569"/>
            <circle cx="16" cy="12" r="1.5" fill="#e2e8f0"/>
        </svg>`,
        fill_dirt: `<svg viewBox="0 0 32 32" class="w-8 h-8 drop-shadow-md">
            <rect x="4" y="5" width="24" height="22" rx="3" fill="#795548" stroke="#3e2723" stroke-width="2"/>
            <line x1="4" y1="12" x2="28" y2="12" stroke="#3e2723" stroke-width="2"/>
            <line x1="4" y1="19" x2="28" y2="19" stroke="#3e2723" stroke-width="2"/>
            <line x1="16" y1="5" x2="16" y2="12" stroke="#3e2723" stroke-width="2"/>
            <line x1="10" y1="12" x2="10" y2="19" stroke="#3e2723" stroke-width="2"/>
            <line x1="22" y1="12" x2="22" y2="19" stroke="#3e2723" stroke-width="2"/>
            <line x1="16" y1="19" x2="16" y2="27" stroke="#3e2723" stroke-width="2"/>
            <rect x="5" y="6" width="10" height="5" fill="#8d6e63" opacity="0.6"/>
        </svg>`,
        wood: `<svg viewBox="0 0 32 32" class="w-8 h-8 drop-shadow-md">
            <path d="M5 26 Q12 18 16 13 Q20 8 27 4" stroke="#3e2723" stroke-width="7" stroke-linecap="round" fill="none"/>
            <path d="M5 26 Q12 18 16 13 Q20 8 27 4" stroke="#795548" stroke-width="4" stroke-linecap="round" fill="none"/>
            <path d="M14 16 Q21 19 28 25 M18 12 Q13 7 8 6 M11 22 Q16 26 18 29" stroke="#5d4037" stroke-width="3" stroke-linecap="round" fill="none"/>
            <path d="M6 25 Q12 18 16 13 Q20 8 26 4" stroke="#a1887f" stroke-width="1.5" stroke-linecap="round" fill="none"/>
        </svg>`,
        withered_wood: `<svg viewBox="0 0 32 32" class="w-8 h-8 drop-shadow-md">
            <path d="M7 27 L15 16 L13 5 M15 16 L26 9 M19 14 L26 21 M8 15 L12 11" stroke="#334155" stroke-width="5" stroke-linecap="round" fill="none"/>
            <path d="M7 27 L15 16 L13 5 M15 16 L26 9 M19 14 L26 21 M8 15 L12 11" stroke="#cbd5e1" stroke-width="2.5" stroke-linecap="round" fill="none"/>
            <path d="M7 27 L15 16 L13 5" stroke="#ffffff" stroke-width="1" stroke-linecap="round" fill="none" opacity="0.6"/>
        </svg>`,
        mazerian: `<svg viewBox="0 0 32 32" class="w-8 h-8 drop-shadow-md">
            <polygon points="16,3 27,12 23,26 9,26 5,12" fill="#9333ea" stroke="#3b0764" stroke-width="2" stroke-linejoin="round"/>
            <polygon points="16,3 27,12 16,16 5,12" fill="#f3e8ff"/>
            <polygon points="16,16 23,26 9,26" fill="#581c87"/>
            <circle cx="12" cy="11" r="2" fill="#ffffff"/>
            <circle cx="20" cy="11" r="2" fill="#ffffff"/>
        </svg>`
    };

    return icons[id] || `<span class="text-2xl drop-shadow-md">📦</span>`;
}

// アイテムのメタデータ
const ITEM_DATA = {
    'sword_junk': { name: 'がらくたの剣', icon: '🗡️', type: 'weapon', tier: 0, atk: 10, maxDurability: 50 },
    'sword_dirt': { name: '土の剣', icon: '🗡️', type: 'weapon', tier: 1, atk: 15, maxDurability: 80 },
    'sword_wood': { name: '木の剣', icon: '🗡️', type: 'weapon', tier: 2, atk: 20, maxDurability: 120 },
    'sword_mud': { name: '泥の剣', icon: '🗡️', type: 'weapon', tier: 2, atk: 35, maxDurability: 50 },
    'sword_ash': { name: '灰の剣', icon: '🗡️', type: 'weapon', tier: 2, atk: 50, maxDurability: Infinity },
    'sword_withered': { name: '廃離の木の剣', icon: '🗡️', type: 'weapon', tier: 3, atk: 33, maxDurability: 400 },
    'sword_rock': { name: '石の剣', icon: '🗡️', type: 'weapon', tier: 3, atk: 23, maxDurability: 150 },
    'sword_iron': { name: '鉄の剣', icon: '🗡️', type: 'weapon', tier: 4, atk: 27, maxDurability: 200 },
    'sword_maze': { name: 'メイズの剣', icon: '🗡️', type: 'weapon', tier: 5, atk: 30, maxDurability: 250 },
    'sword_medusa': { name: 'メデューサの剣', icon: '🗡️', type: 'weapon', tier: 3, atk: 12, maxDurability: 45, petrify: true },
    'geera_axe': { name: 'ギーラアックス', icon: '🪓', type: 'weapon', tier: 4, atk: 30, maxDurability: 500 }, // 動くほど火力UP (最大75)

    'pickaxe_junk': { name: 'がらくたのつるはし', icon: '⛏️', type: 'tool', tier: 0, power: 1, maxDurability: 250 },
    'pickaxe_dirt': { name: '土のつるはし', icon: '⛏️', type: 'tool', tier: 1, power: 1.2, maxDurability: 300 },
    'pickaxe_wood': { name: '木のつるはし', icon: '⛏️', type: 'tool', tier: 2, power: 1.5, maxDurability: 350 },
    'pickaxe_mud': { name: '泥のつるはし', icon: '⛏️', type: 'tool', tier: 2, power: 2.7, maxDurability: 140 },
    'pickaxe_ash': { name: '灰のつるはし', icon: '⛏️', type: 'tool', tier: 2, power: 2.9, maxDurability: Infinity },
    'pickaxe_withered': { name: '廃離の木のつるはし', icon: '⛏️', type: 'tool', tier: 3, power: 2.6, maxDurability: 500 },
    'pickaxe_rock': { name: '石のつるはし', icon: '⛏️', type: 'tool', tier: 3, power: 1.8, maxDurability: 400 },
    'pickaxe_iron': { name: '鉄のつるはし', icon: '⛏️', type: 'tool', tier: 4, power: 2.0, maxDurability: 450 },
    'pickaxe_maze': { name: 'メイズのつるはし', icon: '⛏️', type: 'tool', tier: 5, power: 2.5, maxDurability: 500 },
    'pickaxe_medusa': { name: 'メデューサのつるはし', icon: '⛏️', type: 'tool', tier: 3, power: 1.3, maxDurability: 60 },

    'maze_pickaxe': { name: 'メイズアンチ', icon: '🔱', type: 'tool', power: 10, maxDurability: 2000 },
    'shield_wood': { name: '木の盾', icon: '🛡️', type: 'shield', reduce: 0.1, maxDurability: 20 },
    'shield_rock': { name: '石の盾', icon: '🛡️', type: 'shield', reduce: 0.2, maxDurability: 35 },
    'shield_iron': { name: '鉄の盾', icon: '🛡️', type: 'shield', reduce: 0.35, maxDurability: 45 },
    'shield_mud': { name: '泥の盾', icon: '🛡️', type: 'shield', reduce: 0.4, maxDurability: 65 },
    'shield_ash': { name: '灰の盾', icon: '🛡️', type: 'shield', reduce: 0.8, maxDurability: Infinity },
    'shield_withered': { name: '廃離の木の盾', icon: '🛡️', type: 'shield', reduce: 0.4, maxDurability: 150 },
    'shield_medusa': { name: 'メデューサの盾', icon: '🛡️', type: 'shield', reduce: 0.15, maxDurability: 25, petrify: true },
    'maze_shield': { name: 'メイズの盾', icon: '🛡️', type: 'shield', reduce: 0.5, maxDurability: 55 },

    // 防具データ追加
    'helmet_wood': { name: '木の兜', type: 'helmet', reduce: 0.03, maxDurability: 40 },
    'chestplate_wood': { name: '木の鎧', type: 'chestplate', reduce: 0.05, maxDurability: 60 },
    'boots_wood': { name: '木の靴', type: 'boots', reduce: 0.02, maxDurability: 40 },
    'helmet_rock': { name: '石の兜', type: 'helmet', reduce: 0.05, maxDurability: 100 },
    'chestplate_rock': { name: '石の鎧', type: 'chestplate', reduce: 0.08, maxDurability: 150 },
    'boots_rock': { name: '石の靴', type: 'boots', reduce: 0.04, maxDurability: 100 },
    'helmet_iron': { name: '鉄の兜', type: 'helmet', reduce: 0.1, maxDurability: 200 },
    'chestplate_iron': { name: '鉄の鎧', type: 'chestplate', reduce: 0.15, maxDurability: 300 },
    'boots_iron': { name: '鉄の靴', type: 'boots', reduce: 0.08, maxDurability: 200 },
    'helmet_mud': { name: '泥の兜', type: 'helmet', reduce: 0.07, maxDurability: 80 },
    'chestplate_mud': { name: '泥の鎧', type: 'chestplate', reduce: 0.12, maxDurability: 120 },
    'boots_mud': { name: '泥の靴', type: 'boots', reduce: 0.06, maxDurability: 80 },
    'helmet_ash': { name: '灰の兜', type: 'helmet', reduce: 0.04, maxDurability: Infinity },
    'chestplate_ash': { name: '灰の鎧', type: 'chestplate', reduce: 0.06, maxDurability: Infinity },
    'boots_ash': { name: '灰の靴', type: 'boots', reduce: 0.03, maxDurability: Infinity },
    'helmet_withered': { name: '廃離の木の兜', type: 'helmet', reduce: 0.08, maxDurability: 250 },
    'chestplate_withered': { name: '廃離の木の鎧', type: 'chestplate', reduce: 0.14, maxDurability: 350 },
    'boots_withered': { name: '廃離の木の靴', type: 'boots', reduce: 0.07, maxDurability: 250 },
    'helmet_maze': { name: 'メイズの兜', type: 'helmet', reduce: 0.15, maxDurability: 400 },
    'chestplate_maze': { name: 'メイズの鎧', type: 'chestplate', reduce: 0.25, maxDurability: 600 },
    'boots_maze': { name: 'メイズの靴', type: 'boots', reduce: 0.12, maxDurability: 400 },
    'helmet_medusa': { name: 'メデューサの兜', type: 'helmet', reduce: 0.03, maxDurability: 30, petrify: true },
    'chestplate_medusa': { name: 'メデューサの鎧', type: 'chestplate', reduce: 0.05, maxDurability: 45, petrify: true },
    'boots_medusa': { name: 'メデューサの靴', type: 'boots', reduce: 0.02, maxDurability: 30, petrify: true },

    'compass': { name: 'コンパス', icon: '🧭', type: 'consumable' },
    'heiza_compass': { name: 'ヘイザのコンパス', icon: '🧭', type: 'consumable' },
    'withered_compass': { name: '廃離のコンパス', icon: '🧭', type: 'consumable' },
    'maze_summon': { name: 'メイズサモン', icon: '📜', type: 'consumable' },
    'medusa_eye': { name: 'メデューサアイ', icon: '👁️', type: 'active_item' },
    'dirt': { name: '土', icon: '🟫', type: 'block', tile: TILE.DIRT },
    'ash': { name: '灰', icon: '🌫️', type: 'block', tile: TILE.ASH },
    'rock': { name: '石', icon: '🪨', type: 'block', tile: TILE.ROCK },
    'iron': { name: '鉄', icon: '⚙️', type: 'block', tile: TILE.IRON },
    'fill_dirt': { name: '埋め立て土', icon: '🧱', type: 'fill_block' },
    'wood': { name: '木', icon: '🌿', type: 'material' },
    'withered_wood': { name: '廃離の木', icon: '🪵', type: 'material' },
    'mud': { name: '泥', icon: '🟤', type: 'material' },
    'maze_ingot': { name: 'メイズインゴット', icon: '🟪', type: 'material' },
    'mazerian': { name: 'メイジリアン', icon: '🔮', type: 'material' },
    'geera_plate': { name: 'ギーラの石板', icon: '🪨', type: 'material' },
    'geera_scale': { name: 'ギーラの鱗', icon: '🦎', type: 'material' },
    'medusa_plate': { name: 'メデューサの石板', icon: '🪨', type: 'material' },
    'medusa_hair': { name: 'メデューサの髪', icon: '🐍', type: 'material' },
    'workbench': { name: '木の作業台', icon: '🪚', type: 'block', tile: TILE.WORKBENCH },
    'workbench_rock': { name: '石の作業台', icon: '🪨', type: 'block', tile: TILE.WORKBENCH_ROCK },
    'workbench_swamp': { name: '沼地の作業台', icon: '🐸', type: 'block', tile: TILE.WORKBENCH_SWAMP },
    'workbench_withered': { name: '廃離の作業台', icon: '🌫️', type: 'block', tile: TILE.WORKBENCH_WITHERED },
    'anvil': { name: '金床', icon: '🗜️', type: 'block', tile: TILE.ANVIL },
    'dirt_chest': { name: '土のチェスト', icon: '🧰', type: 'block', tile: TILE.DIRT_CHEST, maxSlots: 6 },
    'wood_chest': { name: '木のチェスト', icon: '🧰', type: 'block', tile: TILE.WOOD_CHEST, maxSlots: 10 },
    'rock_chest': { name: '石のチェスト', icon: '🧰', type: 'block', tile: TILE.ROCK_CHEST, maxSlots: 15 },
    'iron_chest': { name: '鉄のチェスト', icon: '🧰', type: 'block', tile: TILE.IRON_CHEST, maxSlots: 20 },
    'mud_chest': { name: '泥のチェスト', icon: '🧰', type: 'block', tile: TILE.MUD_CHEST, maxSlots: 1 },
    'ash_chest': { name: '灰のチェスト', icon: '🧰', type: 'block', tile: TILE.ASH_CHEST, maxSlots: 20 },
    'withered_chest': { name: '廃離の木のチェスト', icon: '🧰', type: 'block', tile: TILE.WITHERED_CHEST, maxSlots: 25 },
    'maze_chest': { name: 'メイズチェスト', icon: '🧰', type: 'block', tile: TILE.MAZE_CHEST, maxSlots: 30 },
    'geera_chest': { name: 'ギーラチェスト', icon: '🧰', type: 'block', tile: TILE.GEERA_CHEST, maxSlots: 5 },
    'medusa_chest': { name: 'メデューサチェスト', icon: '🧰', type: 'block', tile: TILE.MEDUSA_CHEST, maxSlots: 5 }
};

const SWORD_TIERS = ['sword_junk', 'sword_dirt', 'sword_wood', 'sword_rock', 'sword_iron', 'sword_maze', 'geera_axe'];
const PICKAXE_TIERS = ['pickaxe_junk', 'pickaxe_dirt', 'pickaxe_wood', 'pickaxe_rock', 'pickaxe_iron', 'pickaxe_maze'];

// --- レシピ式クラフトデータ ---
let currentCraftType = '';
const CRAFT_RECIPES = {
    'statue': [
        { id: 'workbench', name: '木の作業台', cost: { wood: 5 } },
        { id: 'maze_pin', name: '迷宮のピン', cost: { rock: 10 }, unique: true, check: () => player.hasMazePin, onCraft: () => { player.hasMazePin = true; showDialog("マップ更新", "迷宮の位置をミニマップに記録した！<br>（ミニマップの拡大/縮小ボタンで全体マップが見れるぞ）"); } }
    ],
    'wood': [
        { id: 'sword_dirt', name: '土の剣', cost: { dirt: 10 } },
        { id: 'pickaxe_dirt', name: '土のつるはし', cost: { dirt: 10 } },
        { id: 'sword_wood', name: '木の剣', cost: { wood: 10 } },
        { id: 'pickaxe_wood', name: '木のつるはし', cost: { wood: 10 } },
        { id: 'shield_wood', name: '木の盾', cost: { wood: 15 } },
        { id: 'workbench_rock', name: '石の作業台', cost: { rock: 10, wood: 5 } },
        { id: 'fill_dirt', name: '埋め立て土', cost: { dirt: 2 } },
        { id: 'heiza_compass', name: 'ヘイザのコンパス', cost: { dirt: 15 } },
        { id: 'dirt_chest', name: '土のチェスト', cost: { dirt: 20 } },
        { id: 'wood_chest', name: '木のチェスト', cost: { wood: 20 } }
    ],
    'rock': [
        { id: 'sword_rock', name: '石の剣', cost: { rock: 15 } },
        { id: 'pickaxe_rock', name: '石のつるはし', cost: { rock: 15 } },
        { id: 'shield_rock', name: '石の盾', cost: { rock: 20 } },
        { id: 'sword_iron', name: '鉄の剣', cost: { iron: 15 } },
        { id: 'pickaxe_iron', name: '鉄のつるはし', cost: { iron: 15 } },
        { id: 'shield_iron', name: '鉄の盾', cost: { iron: 20 } },
        { id: 'workbench_swamp', name: '沼地の作業台', cost: { mud: 10, rock: 5 } },
        { id: 'workbench_withered', name: '廃離の作業台', cost: { withered_wood: 10, rock: 5 } },
        { id: 'anvil', name: '金床', cost: { iron: 10, rock: 10 } },
        { id: 'maze_ingot', name: 'メイズインゴット', cost: { mazerian: 2, rock: 5 } },
        { id: 'sword_maze', name: 'メイズの剣', cost: { maze_ingot: 5 } },
        { id: 'pickaxe_maze', name: 'メイズのつるはし', cost: { maze_ingot: 5 } },
        { id: 'maze_shield', name: 'メイズの盾', cost: { maze_ingot: 8 } },
        { id: 'rock_chest', name: '石のチェスト', cost: { rock: 20 } },
        { id: 'iron_chest', name: '鉄のチェスト', cost: { iron: 20 } },
        { id: 'maze_chest', name: 'メイズチェスト', cost: { maze_ingot: 10 } }
    ],
    'swamp': [
        { id: 'sword_mud', name: '泥の剣', cost: { mud: 15 } },
        { id: 'pickaxe_mud', name: '泥のつるはし', cost: { mud: 15 } },
        { id: 'shield_mud', name: '泥の盾', cost: { mud: 20 } },
        { id: 'mud_chest', name: '泥のチェスト', cost: { mud: 20 } }
    ],
    'withered': [
        { id: 'sword_ash', name: '灰の剣', cost: { ash: 15 } },
        { id: 'pickaxe_ash', name: '灰のつるはし', cost: { ash: 15 } },
        { id: 'shield_ash', name: '灰の盾', cost: { ash: 20 } },
        { id: 'sword_withered', name: '廃離の木の剣', cost: { withered_wood: 15 } },
        { id: 'pickaxe_withered', name: '廃離の木のつるはし', cost: { withered_wood: 15 } },
        { id: 'shield_withered', name: '廃離の木の盾', cost: { withered_wood: 20 } },
        { id: 'sword_medusa', name: 'メデューサの剣', cost: { medusa_hair: 5, withered_wood: 5 } },
        { id: 'pickaxe_medusa', name: 'メデューサのつるはし', cost: { medusa_hair: 5, withered_wood: 5 } },
        { id: 'shield_medusa', name: 'メデューサの盾', cost: { medusa_hair: 6, withered_wood: 5 } },
        { id: 'ash_chest', name: '灰のチェスト', cost: { ash: 20 } },
        { id: 'withered_chest', name: '廃離の木のチェスト', cost: { withered_wood: 20 } }
    ],
    'anvil': [
        { id: 'helmet_wood', name: '木の兜', cost: { wood: 5 } },
        { id: 'chestplate_wood', name: '木の鎧', cost: { wood: 8 } },
        { id: 'boots_wood', name: '木の靴', cost: { wood: 4 } },
        { id: 'helmet_rock', name: '石の兜', cost: { rock: 5 } },
        { id: 'chestplate_rock', name: '石の鎧', cost: { rock: 8 } },
        { id: 'boots_rock', name: '石の靴', cost: { rock: 4 } },
        { id: 'helmet_iron', name: '鉄の兜', cost: { iron: 5 } },
        { id: 'chestplate_iron', name: '鉄の鎧', cost: { iron: 8 } },
        { id: 'boots_iron', name: '鉄の靴', cost: { iron: 4 } },
        { id: 'helmet_mud', name: '泥の兜', cost: { mud: 5 } },
        { id: 'chestplate_mud', name: '泥の鎧', cost: { mud: 8 } },
        { id: 'boots_mud', name: '泥の靴', cost: { mud: 4 } },
        { id: 'helmet_ash', name: '灰の兜', cost: { ash: 5 } },
        { id: 'chestplate_ash', name: '灰の鎧', cost: { ash: 8 } },
        { id: 'boots_ash', name: '灰の靴', cost: { ash: 4 } },
        { id: 'helmet_withered', name: '廃離の木の兜', cost: { withered_wood: 5 } },
        { id: 'chestplate_withered', name: '廃離の木の鎧', cost: { withered_wood: 8 } },
        { id: 'boots_withered', name: '廃離の木の靴', cost: { withered_wood: 4 } },
        { id: 'helmet_maze', name: 'メイズの兜', cost: { maze_ingot: 5 } },
        { id: 'chestplate_maze', name: 'メイズの鎧', cost: { maze_ingot: 8 } },
        { id: 'boots_maze', name: 'メイズの靴', cost: { maze_ingot: 4 } },
        { id: 'helmet_medusa', name: 'メデューサの兜', cost: { medusa_hair: 3, withered_wood: 2 } },
        { id: 'chestplate_medusa', name: 'メデューサの鎧', cost: { medusa_hair: 5, withered_wood: 3 } },
        { id: 'boots_medusa', name: 'メデューサの靴', cost: { medusa_hair: 2, withered_wood: 2 } }
    ]
};

// --- グローバルステート ---
let map = [];
let blockHp = []; // ブロックの耐久度
let explored = [];
let npcs = [];
let activeBombs = [];
let mazeUnreals = []; // メイズアンリアル装置のリスト
let goats = []; // 追加: 山羊リスト
let bulls = []; // 追加: ブル（牛）リスト
let frogs = []; // 追加: カエルリスト
let mazerians = []; // 追加: メイズレアンリスト
let forgottenTrees = []; // 追加: 忘却の木リスト
let witheredForestInfo = null; // 追加: 廃離の森の位置情報
let frogKillCount = 0; // 追加: カエルの討伐数
let projectiles = []; // 追加: 飛び道具リスト
let particles = [];
let damageTexts = [];
let camX = 0, camY = 0;
let screenShake = 0;
let whiteFlashAlpha = 0; // 白化の呪いフラッシュ演出用
let minimapZoomLevel = 1; // 1: 標準, 2: 拡大, 3: 全体
let fullMapZoom = 1.0; // 全体マップ表示中のズーム倍率
let fullMapCenterX = MAP_W / 2; // 全体マップ表示中の中心X（タイル座標）
let fullMapCenterY = MAP_H / 2; // 全体マップ表示中の中心Y（タイル座標）
let isPaused = true; // ホーム画面表示中は一時停止
let isGameStarted = false; // ゲーム開始済みフラグ
let controlMode = 'pc'; // 'pc' または 'mobile'
let mobileJoyVec = { x: 0, y: 0 }; // モバイル用ジョイスティック入力ベクトル
let mobileActionHeld = false; // モバイル用アクションボタン長押しフラグ
let isNoclip = false; // 管理者用壁貫通フラグ

let placedChests = {}; // キー: "x,y", 値: { type: 'dirt_chest'|'wood_chest', items: [ {id, count, durability}, ... ] }
let droppedItems = []; // アイテムドロップ配列 {x, y, vx, vy, id, count, durability, pickupDelay}

// プレイヤー情報
let player = {
    px: 250 * TS + TS/2, // プレースホルダー（後でランダム化）
    py: 250 * TS + TS/2 + TS,
    dir: 'down',
    speed: 5,
    speedDebuffTimer: 0, // 追加: 速度低下デバフのタイマー
    petrifyTimer: 0, // 追加: 白化の呪い（行動不能）タイマー
    stunTimer: 0, // 追加: スタン（行動不能）タイマー
    hp: 120, maxHp: 120,
    attackPower: 25,
    pickaxePower: 1,
    continuousMoveTime: 0, // 追加: ギーラアックス用移動時間
    medusaEyeCooldown: 0, // 追加: メデューサアイのCD
    hasCompass: false,
    hasMazePin: false,
    hasHeizaCompass: false,
    hasWitheredCompass: false,
    cores: 0,
    bossFragments: 0,
    swordTier: 0, // ※旧UIの名残（内部的には保持）
    pickaxeTier: 0, // ※旧UIの名残（内部的には保持）
    durability: {}, // 各アイテムの耐久度管理
    equipment: { helmet: null, chestplate: null, boots: null }, // 追加: 装備スロット
    inventory: { 
        sword_junk: 1, sword_dirt: 0, sword_wood: 0, sword_rock: 0, sword_iron: 0, sword_maze: 0,
        sword_mud: 0, sword_ash: 0, sword_withered: 0, sword_medusa: 0,
        pickaxe_junk: 1, pickaxe_dirt: 0, pickaxe_wood: 0, pickaxe_rock: 0, pickaxe_iron: 0, pickaxe_maze: 0,
        pickaxe_mud: 0, pickaxe_ash: 0, pickaxe_withered: 0, pickaxe_medusa: 0,
        shield_wood: 0, shield_rock: 0, shield_iron: 0, shield_mud: 0, shield_ash: 0, shield_withered: 0, shield_medusa: 0,
        maze_pickaxe: 0, maze_shield: 0, compass: 0, heiza_compass: 0, withered_compass: 0, maze_summon: 0,
        dirt: 0, ash: 0, rock: 0, iron: 0, wood: 0, withered_wood: 0, mazerian: 0, 
        workbench: 0, workbench_rock: 0, workbench_swamp: 0, workbench_withered: 0, anvil: 0,
        fill_dirt: 0, mud: 0, maze_ingot: 0, dirt_chest: 0, wood_chest: 0,
        rock_chest: 0, iron_chest: 0, mud_chest: 0, ash_chest: 0, withered_chest: 0, maze_chest: 0,
        geera_axe: 0, geera_plate: 0, geera_scale: 0, geera_chest: 0,
        medusa_eye: 0, medusa_plate: 0, medusa_hair: 0, medusa_chest: 0
    },
    hotbar: ['sword_junk', 'pickaxe_junk', null, null, null], // 5スロット
    selectedSlot: 0, // 0〜4
    activeStatues: { north: false, south: false, east: false, west: false }
};

// 石像の配置 (中心座標を指定。initMapでランダム化)
let statues = [
    { id: 'center', x: 250, y: 250, type: 'center', color: '#f1c40f', name: '始まりの石像' },
    { id: 'north', x: 250, y: 235, type: 'north', color: '#3498db', name: '北の石像' },
    { id: 'south', x: 250, y: 265, type: 'south', color: '#e74c3c', name: '南の石像' },
    { id: 'east', x: 265, y: 250, type: 'east', color: '#2ecc71', name: '東の石像' },
    { id: 'west', x: 235, y: 250, type: 'west', color: '#9b59b6', name: '西の石像' }
];

// 各石像を守るボス（守護者）(座標はinitMapでランダム化)
let bosses = [
    { id: 'north_boss', x: 250, y: 190, hp: 300, maxHp: 300, active: true, dead: false, drop: 1, targetStatue: 'north', color: '#e67e22', name: 'ギーラ', state: 'idle', stateTimer: 0, vx: 0, vy: 0, facing: 1 },
    { id: 'medusa', x: 100, y: 250, hp: 350, maxHp: 350, active: true, dead: false, drop: 1, targetStatue: 'west', color: '#2ecc71', name: 'メデューサ', state: 'idle', stateTimer: 0, vx: 0, vy: 0, facing: 1, hitCount: 0, ultReady: false, ultUseCount: 0, burstRemaining: 0, burstTimer: 0, shootAngle: 0 }
];

// ラスボス (座標はinitMapでランダム化)
let finalBoss = { 
    x: 450, y: 50, 
    hp: 200, maxHp: 200, 
    active: false, dead: false,
    color: '#2c3e50', name: 'マスターコア'
};

// 入力管理
const keys = {};
window.addEventListener('keydown', e => { keys[e.code] = true; keys[e.key] = true; });
window.addEventListener('keyup', e => { keys[e.code] = false; keys[e.key] = false; });

// --- ホーム画面 & 設定管理 ---
function startGame() {
    isGameStarted = true;
    isPaused = false;
    document.getElementById('homeOverlay').classList.add('hidden');
    document.getElementById('btnReturnHome').classList.remove('hidden');
    applyControlModeUI();
}

function openSettingsMenu() {
    isPaused = true;
    updateSettingsUI();
    document.getElementById('settingsOverlay').classList.remove('hidden');
}

function closeSettingsMenu() {
    document.getElementById('settingsOverlay').classList.add('hidden');
    if (isGameStarted) isPaused = false;
    applyControlModeUI();
}

function returnToHome() {
    document.getElementById('settingsOverlay').classList.add('hidden');
    document.getElementById('homeOverlay').classList.remove('hidden');
    document.getElementById('btnReturnHome').classList.add('hidden');
    isPaused = true;
}

function setControlMode(mode) {
    controlMode = mode;
    updateSettingsUI();
}

function updateSettingsUI() {
    let btnPC = document.getElementById('btnModePC');
    let btnMobile = document.getElementById('btnModeMobile');
    let homeBadge = document.getElementById('homeDeviceBadge');
    let homeText = document.getElementById('homeDeviceText');

    if (controlMode === 'pc') {
        btnPC.className = "p-4 rounded-xl border-2 border-cyan-400 bg-cyan-950/60 text-white flex flex-col items-center gap-2 transition shadow-[0_0_15px_rgba(34,211,238,0.3)]";
        btnMobile.className = "p-4 rounded-xl border-2 border-gray-700 bg-gray-800 text-gray-400 hover:bg-gray-750 flex flex-col items-center gap-2 transition";
        homeBadge.innerText = "PC";
        homeText.innerText = "PC (キーボード & マウス)";
    } else {
        btnMobile.className = "p-4 rounded-xl border-2 border-cyan-400 bg-cyan-950/60 text-white flex flex-col items-center gap-2 transition shadow-[0_0_15px_rgba(34,211,238,0.3)]";
        btnPC.className = "p-4 rounded-xl border-2 border-gray-700 bg-gray-800 text-gray-400 hover:bg-gray-750 flex flex-col items-center gap-2 transition";
        homeBadge.innerText = "モバイル";
        homeText.innerText = "モバイル (タッチ操作)";
    }
}

function applyControlModeUI() {
    let mobileControls = document.getElementById('mobileControls');
    let pcGuide = document.getElementById('pcControlGuide');
    if (controlMode === 'mobile') {
        mobileControls.classList.remove('hidden');
        pcGuide.classList.add('hidden');
    } else {
        mobileControls.classList.add('hidden');
        pcGuide.classList.remove('hidden');
    }
}

// モバイル用バーチャルジョイスティック & ボタンのイベント初期化
function initMobileControls() {
    const zone = document.getElementById('joystickZone');
    const knob = document.getElementById('joystickKnob');
    const actionBtn = document.getElementById('mobileActionBtn');
    let joyTouchId = null;

    function updateJoystick(clientX, clientY) {
        const rect = zone.getBoundingClientRect();
        const centerX = rect.left + rect.width / 2;
        const centerY = rect.top + rect.height / 2;
        const maxRadius = rect.width / 2 - 20;

        let dx = clientX - centerX;
        let dy = clientY - centerY;
        let dist = Math.hypot(dx, dy);

        if (dist > maxRadius) {
            dx = (dx / dist) * maxRadius;
            dy = (dy / dist) * maxRadius;
        }

        knob.style.transform = `translate(${dx}px, ${dy}px)`;

        if (dist > 8) {
            mobileJoyVec.x = dx / maxRadius;
            mobileJoyVec.y = dy / maxRadius;
        } else {
            mobileJoyVec.x = 0;
            mobileJoyVec.y = 0;
        }
    }

    function resetJoystick() {
        joyTouchId = null;
        knob.style.transform = `translate(0px, 0px)`;
        mobileJoyVec.x = 0;
        mobileJoyVec.y = 0;
    }

    zone.addEventListener('touchstart', e => {
        e.preventDefault();
        if (joyTouchId === null && e.changedTouches.length > 0) {
            const t = e.changedTouches[0];
            joyTouchId = t.identifier;
            updateJoystick(t.clientX, t.clientY);
        }
    }, { passive: false });

    zone.addEventListener('touchmove', e => {
        e.preventDefault();
        for (let i = 0; i < e.changedTouches.length; i++) {
            const t = e.changedTouches[i];
            if (t.identifier === joyTouchId) {
                updateJoystick(t.clientX, t.clientY);
                break;
            }
        }
    }, { passive: false });

    zone.addEventListener('touchend', e => {
        for (let i = 0; i < e.changedTouches.length; i++) {
            if (e.changedTouches[i].identifier === joyTouchId) {
                resetJoystick();
                break;
            }
        }
    });

    zone.addEventListener('touchcancel', resetJoystick);

    // アクションボタン（押した瞬間＋押しっぱなし連続採掘/攻撃対応）
    const triggerMobileAction = () => {
        if (isPaused) return;
        let now = Date.now();
        let currentItem = player.hotbar[player.selectedSlot];
        let cooldown = (currentItem && currentItem.startsWith('pickaxe_')) || currentItem === 'maze_pickaxe' ? 200 : 400;
        if (now - lastActionTime > cooldown) {
            lastActionTime = now;
            performAction();
        }
    };

    actionBtn.addEventListener('touchstart', e => {
        e.preventDefault();
        mobileActionHeld = true;
        triggerMobileAction();
    }, { passive: false });

    actionBtn.addEventListener('touchend', e => {
        e.preventDefault();
        mobileActionHeld = false;
    });

    actionBtn.addEventListener('touchcancel', () => {
        mobileActionHeld = false;
    });

    actionBtn.addEventListener('mousedown', () => {
        mobileActionHeld = true;
        triggerMobileAction();
    });
    window.addEventListener('mouseup', () => {
        mobileActionHeld = false;
    });
}

let invSelectedSource = null; // { area: 'bag'|'hotbar', index: number, itemId: string }
let currentOpenedChest = null; // { x, y }

function selectSlot(index) {
    player.selectedSlot = index;
    updateHUD();
}

function openInventory() {
    isPaused = true;
    invSelectedSource = null;
    currentOpenedChest = null;
    
    // UIを1カラムレイアウトにする
    let container = document.getElementById('inventoryContainer');
    container.classList.remove('max-w-3xl');
    container.classList.add('max-w-md');
    
    document.getElementById('chestArea').classList.add('hidden');
    document.getElementById('chestArea').classList.remove('flex');
    document.getElementById('invCloseBtn').classList.remove('hidden');
    
    updateHUD();
    renderMinecraftInventory();
    document.getElementById('inventoryOverlay').classList.remove('hidden');
}

function openChest(x, y) {
    let key = `${x},${y}`;
    let tile = map[y][x];
    if (!placedChests[key]) {
        let type = Object.keys(ITEM_DATA).find(k => ITEM_DATA[k].tile === tile);
        placedChests[key] = {
            type: type,
            items: new Array(ITEM_DATA[type].maxSlots).fill(null),
            timer: 60000 // 泥と灰用タイマー
        };
    }
    isPaused = true;
    invSelectedSource = null;
    currentOpenedChest = { x, y };
    
    let chestData = placedChests[key];
    document.getElementById('chestTitleText').innerText = ITEM_DATA[chestData.type].name;
    
    // UIを2カラムレイアウト（横長）にする
    let container = document.getElementById('inventoryContainer');
    container.classList.remove('max-w-md');
    container.classList.add('max-w-3xl');
    
    document.getElementById('chestArea').classList.remove('hidden');
    document.getElementById('chestArea').classList.add('flex');
    document.getElementById('invCloseBtn').classList.add('hidden');
    
    updateHUD();
    renderMinecraftInventory();
    document.getElementById('inventoryOverlay').classList.remove('hidden');
}

function renderMinecraftInventory() {
    const bagGrid = document.getElementById('inventoryGrid');
    const hbGrid = document.getElementById('inventoryHotbarGrid');
    const chestGrid = document.getElementById('chestGrid');
    const equipHelmetGrid = document.getElementById('equipHelmetGrid');
    const equipChestGrid = document.getElementById('equipChestGrid');
    const equipBootsGrid = document.getElementById('equipBootsGrid');
    const actionBtns = document.getElementById('invActionButtons');
    const infoBar = document.getElementById('invSelectedInfo');
    
    bagGrid.innerHTML = '';
    hbGrid.innerHTML = '';
    equipHelmetGrid.innerHTML = '';
    equipChestGrid.innerHTML = '';
    equipBootsGrid.innerHTML = '';
    if(chestGrid) chestGrid.innerHTML = '';

    // ホットバーに入っていない、かつ装備中でない所持アイテムをバックパックの枠に順番に並べる
    const bagItems = [];
    Object.keys(player.inventory).forEach(itemId => {
        let count = player.inventory[itemId];
        if (count > 0 && ITEM_DATA[itemId] && !player.hotbar.includes(itemId)) {
            // 装備しているアイテムはその分マイナスして考える
            let isEquipped = false;
            for (let slot in player.equipment) {
                if (player.equipment[slot] === itemId) {
                    count--;
                    isEquipped = true;
                }
            }
            if (count > 0) {
                bagItems.push({ id: itemId, drawCount: count });
            }
        }
    });

    // 選択中アイテムの情報表示更新
    if (invSelectedSource && invSelectedSource.itemId) {
        actionBtns.classList.remove('hidden');
        let d = ITEM_DATA[invSelectedSource.itemId];
        let isEquip = invSelectedSource.itemId.startsWith('sword_') || invSelectedSource.itemId.startsWith('pickaxe_') || ['maze_pickaxe', 'maze_shield'].includes(invSelectedSource.itemId) || invSelectedSource.itemId.startsWith('shield_') || ['helmet', 'chestplate', 'boots'].includes(d.type);
        
        let cnt = 0;
        if (invSelectedSource.area === 'chest') {
            let chest = placedChests[`${currentOpenedChest.x},${currentOpenedChest.y}`];
            cnt = chest.items[invSelectedSource.index] ? chest.items[invSelectedSource.index].count : 0;
        } else if (invSelectedSource.area.startsWith('equip_')) {
            cnt = 1; // 装備枠は常に1
        } else {
            // bag または hotbar の場合、純粋な所持数ではなくその枠内にある数として見せるべきだが
            // 簡略化のため inventory 全体の数から装備分を引いた数を表示
            cnt = player.inventory[invSelectedSource.itemId];
            for (let slot in player.equipment) {
                if (player.equipment[slot] === invSelectedSource.itemId) cnt--;
            }
        }
        
        let statText = '';
        if (isEquip && d.maxDurability) {
            let maxD = d.maxDurability;
            let curD = player.durability[invSelectedSource.itemId] !== undefined ? player.durability[invSelectedSource.itemId] : maxD;
            if (invSelectedSource.area === 'chest') {
                let chest = placedChests[`${currentOpenedChest.x},${currentOpenedChest.y}`];
                let itemData = chest.items[invSelectedSource.index];
                if (itemData && itemData.durability !== undefined) curD = itemData.durability;
            }
            let durText = maxD === Infinity ? '∞' : `${curD}/${maxD}`;
            
            if (d.type === 'weapon') statText = `<span class="ml-2 text-red-300">⚔️${d.atk}</span> <span class="ml-1 text-green-400">🔨${durText}</span>`;
            if (d.type === 'shield' || ['helmet', 'chestplate', 'boots'].includes(d.type)) statText = `<span class="ml-2 text-blue-300">🛡️${Math.floor(d.reduce * 100)}%</span> <span class="ml-1 text-green-400">🔨${durText}</span>`;
        }

        infoBar.innerHTML = `<span class="text-cyan-300 font-bold flex items-center">${d.name}${statText}</span><span class="text-yellow-400 font-bold">${isEquip ? '装備品' : '所持/収納: ' + cnt + '個'}</span>`;
    } else {
        actionBtns.classList.add('hidden');
        infoBar.innerHTML = `<span class="text-gray-400">アイテムをタップして選択 → 移動先の枠をタップ</span>`;
    }

    // マイクラ風スロット生成ヘルパー
    const createSlotElement = (area, index, itemId) => {
        const slot = document.createElement('div');
        const isSelected = invSelectedSource && invSelectedSource.area === area && invSelectedSource.index === index;
        const isCurrentActiveHotbar = (area === 'hotbar' && index === player.selectedSlot);

        // マイクラ風の立体ベベル枠スタイル
        let borderStyle = isSelected
            ? 'border-2 border-yellow-400 bg-[#3b4252] scale-105 z-10 shadow-[0_0_12px_rgba(250,204,21,0.5)]'
            : (isCurrentActiveHotbar
                ? 'border-2 border-cyan-400 bg-[#2e3440]'
                : 'border-2 border-t-[#111317] border-l-[#111317] border-b-[#4c566a] border-r-[#4c566a] bg-[#2a2e37] hover:bg-[#353b47]');

        slot.className = `aspect-square rounded-md ${borderStyle} relative flex items-center justify-center cursor-pointer transition select-none`;

        if (area === 'hotbar') {
            slot.innerHTML += `<span class="absolute top-0.5 left-1 text-[10px] font-bold ${isCurrentActiveHotbar ? 'text-cyan-300' : 'text-gray-500'}">${index + 1}</span>`;
        }

        let count = 0;
        let chestItemData = null;
        let displayCount = 0; // 見た目上の数
        
        if (area === 'chest' && currentOpenedChest) {
            let chest = placedChests[`${currentOpenedChest.x},${currentOpenedChest.y}`];
            if (chest && chest.items[index]) {
                count = chest.items[index].count;
                displayCount = count;
                itemId = chest.items[index].id;
                chestItemData = chest.items[index];
            } else {
                itemId = null;
            }
        } else if (area.startsWith('equip_')) {
            let slot = area.replace('equip_', '');
            itemId = player.equipment[slot];
            count = itemId ? 1 : 0;
            displayCount = 1;
        } else if (itemId) {
            count = player.inventory[itemId];
            displayCount = area === 'bag' ? bagItems[index]?.drawCount : count; // hotbarは全所持数表示のままにしておく（変更可能）
        }

        if (itemId && ITEM_DATA[itemId] && count > 0) {
            let isEquip = itemId.startsWith('sword_') || itemId.startsWith('pickaxe_') || ['maze_pickaxe', 'maze_shield'].includes(itemId) || itemId.startsWith('shield_') || ['helmet', 'chestplate', 'boots'].includes(ITEM_DATA[itemId].type);
            
            let durabilityHtml = '';
            let dispCountHtml = '';
            
            if (isEquip && ITEM_DATA[itemId].maxDurability) {
                let maxD = ITEM_DATA[itemId].maxDurability;
                let curD = player.durability[itemId] !== undefined ? player.durability[itemId] : maxD;
                if (chestItemData && chestItemData.durability !== undefined) curD = chestItemData.durability;

                if (maxD === Infinity) {
                    durabilityHtml = `<div class="absolute bottom-0.5 left-1 w-6 h-1 bg-gray-900 rounded-full overflow-hidden"><div class="h-full bg-cyan-400" style="width: 100%"></div></div>`;
                } else {
                    let percent = (curD / maxD) * 100;
                    let color = percent > 50 ? 'bg-green-500' : (percent > 20 ? 'bg-yellow-500' : 'bg-red-500');
                    durabilityHtml = `<div class="absolute bottom-0.5 left-1 w-6 h-1 bg-gray-900 rounded-full overflow-hidden"><div class="h-full ${color}" style="width: ${percent}%"></div></div>`;
                }
                dispCountHtml = `<span class="absolute top-0.5 right-1 text-[9px] font-extrabold text-gray-300 drop-shadow-[0_1px_2px_rgba(0,0,0,1)]">x${displayCount || count}</span>`;
            } else {
                dispCountHtml = `<span class="absolute bottom-0.5 right-1 text-[11px] font-extrabold text-white drop-shadow-[0_1px_2px_rgba(0,0,0,1)]">${displayCount || count}</span>`;
            }

            slot.innerHTML += `
                <div class="w-9 h-9 flex items-center justify-center pointer-events-none">${getItemSvg(itemId)}</div>
                ${durabilityHtml}
                ${dispCountHtml}
            `;
        }

        slot.onclick = () => {
            if (!invSelectedSource) {
                // アイテムがある枠なら選択状態にする
                if (itemId && count > 0) {
                    invSelectedSource = { area, index, itemId };
                    renderMinecraftInventory();
                }
            } else {
                // 同じ枠をもう一度押したら選択解除
                if (invSelectedSource.area === area && invSelectedSource.index === index) {
                    invSelectedSource = null;
                    renderMinecraftInventory();
                    return;
                }

                // 移動・入れ替え処理
                const src = invSelectedSource;
                handleInventoryTransfer(src, { area, index, itemId });

                invSelectedSource = null;
                updateHUD();
                renderMinecraftInventory();
            }
        };

        return slot;
    };

    // 装備枠の生成
    equipHelmetGrid.appendChild(createSlotElement('equip_helmet', 0, null));
    equipChestGrid.appendChild(createSlotElement('equip_chestplate', 0, null));
    equipBootsGrid.appendChild(createSlotElement('equip_boots', 0, null));

    // チェスト枠生成
    if (currentOpenedChest && chestGrid) {
        let chest = placedChests[`${currentOpenedChest.x},${currentOpenedChest.y}`];
        if (chest) {
            for (let i = 0; i < ITEM_DATA[chest.type].maxSlots; i++) {
                chestGrid.appendChild(createSlotElement('chest', i, null));
            }
        }
    }

    // 上段：バックパック20枠（5×4）を生成
    for (let i = 0; i < 20; i++) {
        bagGrid.appendChild(createSlotElement('bag', i, bagItems[i]?.id || null));
    }

    // 下段：ホットバー5枠（1〜5）を生成
    for (let i = 0; i < 5; i++) {
        hbGrid.appendChild(createSlotElement('hotbar', i, player.hotbar[i] || null));
    }
}

function closeInventory() {
    invSelectedSource = null;
    currentOpenedChest = null;
    document.getElementById('inventoryOverlay').classList.add('hidden');
    isPaused = false;
}

// 現在の所持アイテム種類数を取得（最大25種類）
function getOwnedItemTypeCount() {
    let count = 0;
    for (let key in player.inventory) {
        if (player.inventory[key] > 0 && ITEM_DATA[key]) {
            count++;
        }
    }
    return count;
}

// 25種類以内に収まるか判定（すでに所持している種類ならスタック可能）
function canAddItem(id) {
    if (!id || !ITEM_DATA[id]) return false;
    if ((id === 'medusa_eye' || id === 'geera_axe') && (player.inventory[id] || 0) >= 1) return false;
    if ((player.inventory[id] || 0) > 0) return true;
    return getOwnedItemTypeCount() < 25;
}

// アイテムを入手する（26種類目以降は地面にドロップ）
function addItemOrDrop(id, count = 1, dropX = player.px, dropY = player.py, durability = undefined) {
    if (!id || !ITEM_DATA[id]) return false;
    
    // 所持上限チェック
    if ((id === 'medusa_eye' || id === 'geera_axe') && (player.inventory[id] || 0) + count > 1) {
        let addCount = 1 - (player.inventory[id] || 0);
        if (addCount > 0) {
            count = addCount;
        } else {
            spawnDroppedItem(dropX, dropY, id, count, durability, false);
            spawnDamageText(dropX, dropY - 20, "1つしか持てない!");
            return false;
        }
    }

    if (canAddItem(id)) {
        player.inventory[id] = (player.inventory[id] || 0) + count;
        if (durability !== undefined) {
            player.durability[id] = durability;
        } else if (ITEM_DATA[id].maxDurability && player.durability[id] === undefined) {
            player.durability[id] = ITEM_DATA[id].maxDurability;
        }
        // バックパック20枠から溢れて隠れないようホットバーに空きがあれば自動配置
        if (!player.hotbar.includes(id)) {
            let bagCount = Object.keys(player.inventory).filter(k => player.inventory[k] > 0 && ITEM_DATA[k] && !player.hotbar.includes(k)).length;
            if (bagCount > 20) {
                let emptySlot = player.hotbar.indexOf(null);
                if (emptySlot !== -1) {
                    player.hotbar[emptySlot] = id;
                }
            }
        }
        return true;
    } else {
        spawnDroppedItem(dropX, dropY, id, count, durability, false);
        spawnDamageText(dropX, dropY - 20, "カバン満杯! ドロップ");
        return false;
    }
}

function handleInventoryTransfer(src, dest) {
    let srcChest = src.area === 'chest' ? placedChests[`${currentOpenedChest.x},${currentOpenedChest.y}`] : null;
    let destChest = dest.area === 'chest' ? placedChests[`${currentOpenedChest.x},${currentOpenedChest.y}`] : null;

    let srcEquipType = src.area.startsWith('equip_') ? src.area.replace('equip_', '') : null;
    let destEquipType = dest.area.startsWith('equip_') ? dest.area.replace('equip_', '') : null;

    // 装備枠絡みの移動
    if (srcEquipType || destEquipType) {
        if (srcEquipType && !destEquipType) { // 装備を外す
            let equipId = player.equipment[srcEquipType];
            player.equipment[srcEquipType] = null;
            if (dest.area === 'hotbar') {
                let old = player.hotbar[dest.index];
                if (old && ITEM_DATA[old] && ITEM_DATA[old].type === srcEquipType) { 
                    player.equipment[srcEquipType] = old;
                }
                player.hotbar[dest.index] = equipId;
            }
        } else if (!srcEquipType && destEquipType) { // 装備する
            let itemData = ITEM_DATA[src.itemId];
            if (!itemData || itemData.type !== destEquipType) {
                spawnDamageText(player.px, player.py - 20, "部位が違う!");
                return;
            }
            let oldEquip = player.equipment[destEquipType];
            if (src.area === 'hotbar') {
                player.hotbar[src.index] = oldEquip || null;
            }
            player.equipment[destEquipType] = src.itemId;
        }
        return;
    }

    // 同一エリア間（bag <-> hotbar）
    if (src.area !== 'chest' && dest.area !== 'chest') {
        if (src.area === 'hotbar' && dest.area === 'hotbar') {
            const temp = player.hotbar[dest.index];
            player.hotbar[dest.index] = player.hotbar[src.index];
            player.hotbar[src.index] = temp;
        } else if (src.area === 'bag' && dest.area === 'hotbar') {
            player.hotbar[dest.index] = src.itemId;
        } else if (src.area === 'hotbar' && dest.area === 'bag') {
            player.hotbar[src.index] = null;
            if (dest.itemId && player.inventory[dest.itemId] > 0) {
                player.hotbar[src.index] = dest.itemId;
            }
        }
    } 
    // プレイヤー -> チェスト
    else if (src.area !== 'chest' && dest.area === 'chest') {
        let moveCount = player.inventory[src.itemId];
        let moveDurability = player.durability[src.itemId];
        
        if (!destChest.items[dest.index]) {
            destChest.items[dest.index] = { id: src.itemId, count: moveCount, durability: moveDurability };
            player.inventory[src.itemId] -= moveCount;
            if (src.area === 'hotbar') player.hotbar[src.index] = null;
        } else if (destChest.items[dest.index].id === src.itemId) {
            destChest.items[dest.index].count += moveCount;
            player.inventory[src.itemId] -= moveCount;
            if (src.area === 'hotbar') player.hotbar[src.index] = null;
        } else {
            let tempItem = destChest.items[dest.index];
            destChest.items[dest.index] = { id: src.itemId, count: moveCount, durability: moveDurability };
            
            player.inventory[src.itemId] -= moveCount;
            if (src.area === 'hotbar') player.hotbar[src.index] = null;
            
            player.inventory[tempItem.id] = (player.inventory[tempItem.id] || 0) + tempItem.count;
            if (tempItem.durability !== undefined) player.durability[tempItem.id] = tempItem.durability;
            if (src.area === 'hotbar') player.hotbar[src.index] = tempItem.id;
        }
    }
    // チェスト -> プレイヤー
    else if (src.area === 'chest' && dest.area !== 'chest') {
        let moveItem = srcChest.items[src.index];
        if (!moveItem) return;
        if (dest.area === 'hotbar') {
            if (!canAddItem(moveItem.id)) {
                spawnDroppedItem(player.px, player.py, moveItem.id, moveItem.count, moveItem.durability, false);
                let msg = ((moveItem.id === 'medusa_eye' || moveItem.id === 'geera_axe') && (player.inventory[moveItem.id] || 0) >= 1) ? "1つしか持てない! ドロップ" : "カバン満杯! ドロップ";
                spawnDamageText(player.px, player.py - 20, msg);
                srcChest.items[src.index] = null;
                return;
            }
            addItemOrDrop(moveItem.id, moveItem.count, player.px, player.py, moveItem.durability);
            
            let currentHotbarItem = player.hotbar[dest.index];
            if (currentHotbarItem) {
                srcChest.items[src.index] = { id: currentHotbarItem, count: player.inventory[currentHotbarItem], durability: player.durability[currentHotbarItem] };
                player.inventory[currentHotbarItem] = 0; 
            } else {
                srcChest.items[src.index] = null;
            }
            player.hotbar[dest.index] = moveItem.id;
        } else {
            if (!canAddItem(moveItem.id)) {
                spawnDroppedItem(player.px, player.py, moveItem.id, moveItem.count, moveItem.durability, false);
                let msg = ((moveItem.id === 'medusa_eye' || moveItem.id === 'geera_axe') && (player.inventory[moveItem.id] || 0) >= 1) ? "1つしか持てない! ドロップ" : "カバン満杯! ドロップ";
                spawnDamageText(player.px, player.py - 20, msg);
                srcChest.items[src.index] = null;
                return;
            }
            addItemOrDrop(moveItem.id, moveItem.count, player.px, player.py, moveItem.durability);
            srcChest.items[src.index] = null;
        }
    }
    // チェスト内移動
    else if (src.area === 'chest' && dest.area === 'chest') {
        if (!destChest.items[dest.index]) {
            destChest.items[dest.index] = srcChest.items[src.index];
            srcChest.items[src.index] = null;
        } else if (destChest.items[dest.index].id === srcChest.items[src.index].id) {
            destChest.items[dest.index].count += srcChest.items[src.index].count;
            srcChest.items[src.index] = null;
        } else {
            let temp = destChest.items[dest.index];
            destChest.items[dest.index] = srcChest.items[src.index];
            srcChest.items[src.index] = temp;
        }
    }
}

function dropSelectedItem(amount) {
    if (!invSelectedSource || !invSelectedSource.itemId) return;
    
    let sourceId = invSelectedSource.itemId;
    let sourceArea = invSelectedSource.area;
    let sourceIndex = invSelectedSource.index;
    
    let currentCount = 0;
    let currentDurability = undefined;
    
    if (sourceArea === 'bag' || sourceArea === 'hotbar') {
        currentCount = player.inventory[sourceId];
        currentDurability = player.durability[sourceId];
    } else if (sourceArea === 'chest') {
        let chest = placedChests[`${currentOpenedChest.x},${currentOpenedChest.y}`];
        currentCount = chest.items[sourceIndex].count;
        currentDurability = chest.items[sourceIndex].durability;
    }
    
    let dropCount = amount === 'all' ? currentCount : Math.min(amount, currentCount);
    
    if (sourceArea === 'bag' || sourceArea === 'hotbar') {
        player.inventory[sourceId] -= dropCount;
        if (player.inventory[sourceId] <= 0) {
            player.inventory[sourceId] = 0;
            if (sourceArea === 'hotbar') player.hotbar[sourceIndex] = null;
            invSelectedSource = null;
        }
    } else if (sourceArea === 'chest') {
        let chest = placedChests[`${currentOpenedChest.x},${currentOpenedChest.y}`];
        chest.items[sourceIndex].count -= dropCount;
        if (chest.items[sourceIndex].count <= 0) {
            chest.items[sourceIndex] = null;
            invSelectedSource = null;
        }
    }
    
    spawnDroppedItem(player.px, player.py, sourceId, dropCount, currentDurability, true);
    updateHUD();
    renderMinecraftInventory();
}

function spawnDroppedItem(x, y, id, count, durability, isDroppedByPlayer = false) {
    droppedItems.push({
        x: x, y: y,
        vx: (Math.random() - 0.5) * 6,
        vy: (Math.random() - 0.5) * 6,
        id: id, count: count,
        durability: durability,
        pickupDelay: 500, // 0.5秒の基本ディレイ
        justDropped: isDroppedByPlayer, // プレイヤーが捨てたかどうかのフラグ
        lifetime: 60000 // 1分(60000ミリ秒)で自然消滅
    });
}

function updateDroppedItems(dt) {
    for (let i = droppedItems.length - 1; i >= 0; i--) {
        let item = droppedItems[i];
        
        // ライフタイム（自然消滅）の処理
        if (item.lifetime !== undefined) {
            item.lifetime -= dt;
            if (item.lifetime <= 0) {
                droppedItems.splice(i, 1);
                continue;
            }
        }

        item.x += item.vx * (dt/16);
        item.y += item.vy * (dt/16);
        
        item.vx *= 0.85;
        item.vy *= 0.85;
        
        if (checkCollision(item.x, item.y, 10)) {
            item.x -= item.vx * (dt/16);
            item.y -= item.vy * (dt/16);
            item.vx *= -0.5;
            item.vy *= -0.5;
        }
        
        let dist = Math.hypot(player.px - item.x, player.py - item.y);

        // プレイヤーがアイテムを捨てた後、一度離れたら拾えるようにする
        if (item.justDropped && dist > TS * 1.5) {
            item.justDropped = false;
        }
        
        if (item.pickupDelay > 0) {
            item.pickupDelay -= dt;
        } else if (!item.justDropped) { // 捨てた直後でなければ拾える
            if (dist < TS * 1.2) {
                let data = ITEM_DATA[item.id];
                if (!data) {
                    droppedItems.splice(i, 1);
                    continue;
                }
                if (canAddItem(item.id)) {
                    addItemOrDrop(item.id, item.count, item.x, item.y, item.durability);
                    spawnDamageText(player.px, player.py - 20, `${data.name} +${item.count}`);
                    updateHUD();
                    droppedItems.splice(i, 1);
                } else if (!item.warnCooldown || item.warnCooldown <= 0) {
                    if ((item.id === 'medusa_eye' || item.id === 'geera_axe') && (player.inventory[item.id] || 0) >= 1) {
                        spawnDamageText(item.x, item.y - 16, "1つしか持てない!");
                    } else {
                        spawnDamageText(item.x, item.y - 16, "カバン満杯!");
                    }
                    item.warnCooldown = 2000;
                }
            }
        }
        if (item.warnCooldown > 0) {
            item.warnCooldown -= dt;
        }
    }
}

function drawDroppedItems() {
    droppedItems.forEach(item => {
        let mapIdx = Math.floor(item.x / TS);
        let mapIdy = Math.floor(item.y / TS);
        if (mapIdx >= 0 && mapIdx < MAP_W && mapIdy >= 0 && mapIdy < MAP_H) {
            if (!explored[mapIdy][mapIdx]) return;
        }
        
        // 消滅までの残り時間が少ない（3秒未満）なら点滅させる
        if (item.lifetime !== undefined && item.lifetime < 3000) {
            if (Math.floor(Date.now() / 150) % 2 === 0) return;
        }
        
        let dx = item.x - camX;
        let dy = item.y - camY;
        let floatY = Math.sin(Date.now() / 150 + item.x) * 4;
        
        ctx.save();
        ctx.translate(dx, dy + floatY);
        let d = ITEM_DATA[item.id];
        ctx.font = '18px Inter';
        ctx.textAlign = 'center';
        ctx.textBaseline = 'middle';
        ctx.shadowBlur = 4;
        ctx.shadowColor = 'rgba(255,255,255,0.5)';
        ctx.fillText(d ? d.icon : '📦', 0, 0);
        ctx.restore();
    });
}

// --- マップ生成処理 ---
function initMap() {
    // 最初の石像をワールドの完全な中心に配置
    let baseCX = Math.floor(MAP_W / 2);
    let baseCY = Math.floor(MAP_H / 2);

    statues[0].x = baseCX; statues[0].y = baseCY;
    statues[1].x = baseCX; statues[1].y = baseCY - 15; // 北
    statues[2].x = baseCX; statues[2].y = baseCY + 15; // 南
    statues[3].x = baseCX + 15; statues[3].y = baseCY; // 東
    statues[4].x = baseCX - 15; statues[4].y = baseCY; // 西

    player.px = baseCX * TS + TS/2;
    player.py = baseCY * TS + TS/2 + TS;

    // ラスボスを円形マップ内の遠方（中心から160〜250マス）にランダム配置
    let fbAngle = Math.random() * Math.PI * 2;
    let fbDist = 160 + Math.random() * 90;
    finalBoss.x = Math.floor(baseCX + Math.cos(fbAngle) * fbDist);
    finalBoss.y = Math.floor(baseCY + Math.sin(fbAngle) * fbDist);

    // 全面を土で初期化（耐久度3に変更）
    map = new Array(MAP_H).fill(0).map(() => new Array(MAP_W).fill(TILE.DIRT));
    blockHp = new Array(MAP_H).fill(0).map(() => new Array(MAP_W).fill(3)); 
    explored = new Array(MAP_H).fill(0).map(() => new Array(MAP_W).fill(false));
    goats = []; // 山羊リストの初期化
    bulls = []; // ブルリストの初期化
    frogs = []; // カエルリストの初期化
    mazerians = []; // メイズレアンリストの初期化
    forgottenTrees = []; // 忘却の木リストの初期化
    whiteFlashAlpha = 0;
    player.petrifyTimer = 0;
    player.stunTimer = 0;
    player.speedDebuffTimer = 0;
    let lakes = [];
    for(let i=0; i<25; i++) {
        let cx = Math.floor(Math.random() * MAP_W);
        let cy = Math.floor(Math.random() * MAP_H);
        let radius = 6 + Math.random() * 6; // 半径6〜12の湖
        lakes.push({x: cx, y: cy, r: radius});
        for(let y = Math.floor(cy - radius); y <= Math.ceil(cy + radius); y++) {
            for(let x = Math.floor(cx - radius); x <= Math.ceil(cx + radius); x++) {
                if(x >= 0 && x < MAP_W && y >= 0 && y < MAP_H) {
                    let noise = (Math.random() - 0.5) * 3;
                    if(Math.hypot(x-cx, y-cy) <= radius + noise) {
                        map[y][x] = TILE.WATER;
                        blockHp[y][x] = 999;
                    }
                }
            }
        }
    }

    // 川の生成（湖から伸びる）
    lakes.forEach(lake => {
        let numRivers = 1 + Math.floor(Math.random() * 3); // 湖から1〜3本の川
        for(let i=0; i<numRivers; i++) {
            let angle = Math.random() * Math.PI * 2;
            let cx = lake.x + Math.cos(angle) * lake.r;
            let cy = lake.y + Math.sin(angle) * lake.r;
            let length = 20 + Math.random() * 30; // 川の長さを少し戻す
            let width = 0.8 + Math.random() * 0.8; // 川の幅を少し戻す
            
            for(let j=0; j<length; j++) {
                for(let y = Math.floor(cy - width); y <= Math.ceil(cy + width); y++) {
                    for(let x = Math.floor(cx - width); x <= Math.ceil(cx + width); x++) {
                        if(x >= 0 && x < MAP_W && y >= 0 && y < MAP_H) {
                            if(Math.hypot(x-cx, y-cy) <= width + (Math.random()-0.5)) {
                                map[y][x] = TILE.WATER;
                                blockHp[y][x] = 999;
                            }
                        }
                    }
                }
                angle += (Math.random() - 0.5) * 1.2; // くねくね曲がる
                cx += Math.cos(angle) * width * 0.8;
                cy += Math.sin(angle) * width * 0.8;
            }
        }
    });

    // ランダムにクラスター（塊）を生成する関数
    function generateClusters(type, count, maxRadius, maxHp) {
        for(let i=0; i<count; i++) {
            let cx = Math.floor(Math.random() * MAP_W);
            let cy = Math.floor(Math.random() * MAP_H);
            let radius = Math.random() * maxRadius; // 半径を調整して散らばらせる
            for(let y = Math.floor(cy - radius); y <= Math.ceil(cy + radius); y++) {
                for(let x = Math.floor(cx - radius); x <= Math.ceil(cx + radius); x++) {
                    if(x >= 0 && x < MAP_W && y >= 0 && y < MAP_H) {
                        if(Math.hypot(x-cx, y-cy) <= radius) {
                            if (map[y][x] === TILE.DIRT) { // 土の上のみ上書き（水には生成しない）
                                map[y][x] = type;
                                blockHp[y][x] = maxHp;
                            }
                        }
                    }
                }
            }
        }
    }

    // 岩石と鉄鉱石のクラスターを配置 (マップ拡大に合わせて数を増量)
    generateClusters(TILE.ROCK, 3000, 1.2, 5); 
    generateClusters(TILE.IRON, 2000, 0.8, 8); 

    // 直線的（鉱脈状）にブロックを生成する関数
    function generateVeins(type, count, minLen, maxLen, maxHp) {
        for(let i=0; i<count; i++) {
            let cx = Math.floor(Math.random() * MAP_W);
            let cy = Math.floor(Math.random() * MAP_H);
            let length = minLen + Math.floor(Math.random() * (maxLen - minLen));
            // 直線の方向
            let dx = (Math.random() - 0.5) * 2;
            let dy = (Math.random() - 0.5) * 2;
            let len = Math.hypot(dx, dy);
            dx /= len; dy /= len;
            
            for(let j=0; j<length; j++) {
                let x = Math.floor(cx + dx * j);
                let y = Math.floor(cy + dy * j);
                // 少しのブレ（太さ）
                let offsetX = Math.floor((Math.random() - 0.5) * 2.5);
                let offsetY = Math.floor((Math.random() - 0.5) * 2.5);
                x += offsetX; y += offsetY;
                
                if(x >= 0 && x < MAP_W && y >= 0 && y < MAP_H) {
                    if(map[y][x] === TILE.DIRT) { // 土の上のみ上書き
                        map[y][x] = type;
                        blockHp[y][x] = maxHp;
                    }
                }
            }
        }
    }

    // 地下茎を直線的に配置
    generateVeins(TILE.ROOT, 400, 6, 15, 2);

    // 擬態した爆弾岩を直線的に配置（生成頻度も高めに）
    generateVeins(TILE.BOMB, 1000, 5, 12, 3);

    // 洞窟の生成関数（複数の円を繋げて自然な形にする）
    function generateCave(cx, cy, type, hasHeiza) {
        let numCircles = type === 'hay' ? 2 + Math.floor(Math.random() * 3) : 1 + Math.floor(Math.random() * 2); // 干し草の場合は複数の円を繋げて大きくする
        let currentX = cx;
        let currentY = cy;
        
        for(let i=0; i<numCircles; i++) {
            let radius = type === 'hay' ? 4.0 + Math.random() * 3.0 : 1.5 + Math.random() * 1.5; // 干し草洞窟の半径を大幅に拡大
            for(let y = Math.floor(currentY - radius); y <= Math.ceil(currentY + radius); y++) {
                for(let x = Math.floor(currentX - radius); x <= Math.ceil(currentX + radius); x++) {
                    if(x >= 0 && x < MAP_W && y >= 0 && y < MAP_H) {
                        if(Math.hypot(x-currentX, y-currentY) <= radius) {
                            if (map[y][x] !== TILE.WATER) { // 水は洞窟で上書きしない
                                if (type === 'hay') {
                                     map[y][x] = TILE.HAY;
　　　　　　　　} else if (type === 'forest') {
                                     map[y][x] = TILE.FOREST_FLOOR;
　　　　　　　　} else {
                                     map[y][x] = TILE.FLOOR;
　　　　　　　　}

                                blockHp[y][x] = 0;
                            }
                        }
                    }
                }
            }
            // 次の円の中心を少しずらして連結させる
            currentX += (Math.random() - 0.5) * 3;
            currentY += (Math.random() - 0.5) * 3;
        }

        // 干し草エリアの場合、山羊をランダムに生成
        if (type === 'hay' && !hasHeiza) {
            let numGoats = 1 + Math.floor(Math.random() * 3);
            for(let i=0; i<numGoats; i++) {
                goats.push({
                    x: cx + (Math.random()-0.5)*4,
                    y: cy + (Math.random()-0.5)*4,
                    hp: 150, maxHp: 150,
                    hostile: false, attackCount: 0,
                    state: 'idle', stateTimer: 0, vx: 0, vy: 0, facing: 1
                });
            }
        }

        // 通常の洞窟の場合、ブル（牛）を確率で自然スポーン
        if (type === 'empty' && !hasHeiza && Math.hypot(cx - baseCX, cy - baseCY) > 15 && Math.random() < 0.25) {
            let numBulls = 1 + (Math.random() < 0.3 ? 1 : 0);
            for (let i = 0; i < numBulls; i++) {
                let bx = Math.max(2, Math.min(MAP_W - 3, cx + (Math.random() - 0.5) * 2));
                let by = Math.max(2, Math.min(MAP_H - 3, cy + (Math.random() - 0.5) * 2));
                if (map[Math.floor(by)][Math.floor(bx)] === TILE.FLOOR) {
                    bulls.push({
                        x: bx,
                        y: by,
                        hp: 50,
                        maxHp: 50,
                        state: 'idle',
                        stateTimer: 500 + Math.random() * 500,
                        chargeCooldown: 0,
                        vx: 0,
                        vy: 0,
                        facing: 1
                    });
                }
            }
        }

        if(hasHeiza) {
            npcs.push({ x: cx*TS + TS/2, y: cy*TS + TS/2, type: 'heiza' });
        }
    }

    // 洞窟を配置 (ランダム位置)
    // ヘイザはスタート地点から少し離れた場所（半径60〜100マス程度）に配置
    let heizaAngle = Math.random() * Math.PI * 2;
    let heizaDist = 60 + Math.random() * 40;
    let heizaCaveX = Math.floor(baseCX + Math.cos(heizaAngle) * heizaDist);
    let heizaCaveY = Math.floor(baseCY + Math.sin(heizaAngle) * heizaDist);
    // マップ範囲内に収める
    heizaCaveX = Math.max(20, Math.min(MAP_W - 20, heizaCaveX));
    heizaCaveY = Math.max(20, Math.min(MAP_H - 20, heizaCaveY));
    
    generateCave(heizaCaveX, heizaCaveY, 'hay', true);  // 確定ヘイザあり（干し草）
    
    // 円形ワールド内のランダム座標を取得するヘルパー
    function getRandomWorldPos(minDistFromCenter = 8, margin = 8) {
        let maxR = WORLD_RADIUS - margin;
        let angle = Math.random() * Math.PI * 2;
        let r = Math.sqrt(Math.random()) * (maxR - minDistFromCenter) + minDistFromCenter;
        return {
            x: Math.floor(baseCX + Math.cos(angle) * r),
            y: Math.floor(baseCY + Math.sin(angle) * r)
        };
    }

    // 干し草の小洞窟を円形ワールド全体へ均等に行き渡らせる
    for (let gy = baseCY - WORLD_RADIUS + 20; gy <= baseCY + WORLD_RADIUS - 20; gy += 75) {
        for (let gx = baseCX - WORLD_RADIUS + 20; gx <= baseCX + WORLD_RADIUS - 20; gx += 75) {
            let cx = Math.floor(gx + (Math.random() - 0.5) * 50);
            let cy = Math.floor(gy + (Math.random() - 0.5) * 50);
            let distFromCenter = Math.hypot(cx - baseCX, cy - baseCY);
            if (distFromCenter >= 12 && distFromCenter <= WORLD_RADIUS - 10) {
                generateCave(cx, cy, 'hay', false);
            }
        }
    }

    // 細長い洞窟（ワーム型）を生成する関数
    function generateWormCave(startX, startY, length, maxRadius) {
        let cx = startX;
        let cy = startY;
        let angle = Math.random() * Math.PI * 2;
        for(let i=0; i<length; i++) {
            let r = 1.5 + Math.random() * maxRadius;
            for(let y = Math.floor(cy - r); y <= Math.ceil(cy + r); y++) {
                for(let x = Math.floor(cx - r); x <= Math.ceil(cx + r); x++) {
                    if(x >= 0 && x < MAP_W && y >= 0 && y < MAP_H) {
                        if(Math.hypot(x-cx, y-cy) <= r) {
                            if (map[y][x] !== TILE.WATER) { // 水は上書きしない
                                map[y][x] = TILE.FLOOR;
                                blockHp[y][x] = 0;
                            }
                        }
                    }
                }
            }
            angle += (Math.random() - 0.5) * 0.8; // 緩やかに曲がる
            cx += Math.cos(angle) * (r * 0.8);
            cy += Math.sin(angle) * (r * 0.8);
        }
    }

    // 小さな洞窟を円形ワールド全体へ偏りなく行き渡らせる（グリッド分散＋ランダム配置）
    for (let gy = baseCY - WORLD_RADIUS + 12; gy <= baseCY + WORLD_RADIUS - 12; gy += 22) {
        for (let gx = baseCX - WORLD_RADIUS + 12; gx <= baseCX + WORLD_RADIUS - 12; gx += 22) {
            let cx = Math.floor(gx + (Math.random() - 0.5) * 16);
            let cy = Math.floor(gy + (Math.random() - 0.5) * 16);
            let distFromCenter = Math.hypot(cx - baseCX, cy - baseCY);
            if (distFromCenter >= 8 && distFromCenter <= WORLD_RADIUS - 6) {
                generateCave(cx, cy, 'empty', false);
            }
        }
    }
    for (let i = 0; i < 150; i++) {
        let pos = getRandomWorldPos(8, 6);
        generateCave(pos.x, pos.y, 'empty', false);
    }

    // 細長い小洞窟を円形ワールド全体へ配置
    for (let i = 0; i < 35; i++) {
        let pos = getRandomWorldPos(12, 15);
        generateWormCave(pos.x, pos.y, 8 + Math.random() * 10, 1.3);
    }

    // 巨大な繋がっている洞窟（地底湖や大広間）を生成 (スタート地点周辺は避ける)
    // レアな存在にするため数を大幅に削減
    for(let i = 0; i < 3; i++) {
        let pos = getRandomWorldPos(55, 25);
        generateWormCave(pos.x, pos.y, 15 + Math.random() * 15, 2.2);
    }

    // 新バイオーム：湿地帯の生成関数
    function generateSwamp(startX, startY, length, maxRadius) {
        let cx = startX;
        let cy = startY;
        let angle = Math.random() * Math.PI * 2;
        for(let i=0; i<length; i++) {
            let r = 1.5 + Math.random() * maxRadius;
            for(let y = Math.floor(cy - r); y <= Math.ceil(cy + r); y++) {
                for(let x = Math.floor(cx - r); x <= Math.ceil(cx + r); x++) {
                    if(x >= 0 && x < MAP_W && y >= 0 && y < MAP_H) {
                        // スタート地点周辺（半径50マス）は絶対に湿地にしない
                        if (Math.hypot(x - baseCX, y - baseCY) < 50) continue;

                        if(Math.hypot(x-cx, y-cy) <= r) {
                            // 既存の重要施設（迷路、石像の深層土、水、地割れなど）以外を上書き
                            if (map[y][x] !== TILE.MAZE_BRICK && map[y][x] !== TILE.MAZE_UNREAL && map[y][x] !== TILE.DEEP_DIRT && map[y][x] !== TILE.WATER && map[y][x] !== TILE.CHASM) {
                                if (Math.random() < 0.35) { // 水多め
                                    map[y][x] = TILE.SWAMP_WATER;
                                    blockHp[y][x] = 999;
                                } else {
                                    map[y][x] = TILE.MUD; // 地面は全部泥になる
                                    blockHp[y][x] = 2; // 泥の耐久は2
                                    
                                    // カエルの生成
                                    if (Math.random() < 0.03) {
                                        frogs.push({
                                            x: x, y: y,
                                            hp: 150, maxHp: 150,
                                            hostile: false, attackCount: 0,
                                            state: 'idle', stateTimer: 0,
                                            facing: 1, z: 0
                                        });
                                    }
                                }
                            }
                        }
                    }
                }
            }
            angle += (Math.random() - 0.5) * 1.0;
            cx += Math.cos(angle) * (r * 0.8);
            cy += Math.sin(angle) * (r * 0.8);
        }
    }

    // 湿地帯をマップに複数配置 (スタート地点周辺は避ける)
    for(let i = 0; i < 20; i++) {
        let cx, cy;
        do {
            cx = 40 + Math.floor(Math.random() * (MAP_W - 80));
            cy = 40 + Math.floor(Math.random() * (MAP_H - 80));
        } while (Math.hypot(cx - baseCX, cy - baseCY) < 50); // 始まりの石像から50マス以内は再抽選
        generateSwamp(cx, cy, 40 + Math.random() * 40, 6); // 巨大洞窟と同等サイズ
    }

    // 新バイオーム：廃離の森（西側にある巨大な丸形のバイオーム）
    function generateWitheredForest() {
        let forestRadius = 55; // 巨大な円形バイオーム
        let stageRadius = 15;  // 真ん中の何もないステージの半径
        // 円形マップ内の西側に完全に収まる位置に配置
        let forestAngle = Math.PI + (Math.random() - 0.5) * 0.9;
        let forestDist = 135 + Math.random() * 45;
        let forestX = Math.floor(baseCX + Math.cos(forestAngle) * forestDist);
        let forestY = Math.floor(baseCY + Math.sin(forestAngle) * forestDist);
        witheredForestInfo = { x: forestX, y: forestY, radius: forestRadius, spawnTimer: 8000 };

        for (let y = Math.floor(forestY - forestRadius - 5); y <= Math.ceil(forestY + forestRadius + 5); y++) {
            for (let x = Math.floor(forestX - forestRadius - 5); x <= Math.ceil(forestX + forestRadius + 5); x++) {
                if (x >= 0 && x < MAP_W && y >= 0 && y < MAP_H) {
                    // スタート地点周辺（半径40マス）は避ける
                    if (Math.hypot(x - baseCX, y - baseCY) < 40) continue;

                    // 縁に少し自然なゆらぎを持たせた巨大な円形
                    let edgeNoise = Math.sin(x * 0.25) * 2 + Math.cos(y * 0.25) * 2;
                    let dist = Math.hypot(x - forestX, y - forestY);
                    if (dist <= forestRadius + edgeNoise) {
                        if (map[y][x] !== TILE.MAZE_BRICK && map[y][x] !== TILE.MAZE_UNREAL && map[y][x] !== TILE.DEEP_DIRT) {
                            if (dist <= stageRadius) {
                                // 真ん中には何もないステージ（灰色の地面のみ）
                                map[y][x] = TILE.ASH_FLOOR;
                                blockHp[y][x] = 0;
                            } else {
                                let r = Math.random();
                                if (r < 0.08) {
                                    // 木（地下茎）の代わりに廃離の木が生えている
                                    map[y][x] = TILE.WITHERED_TREE;
                                    blockHp[y][x] = 3;
                                } else if (r < 0.16) {
                                    // たまに壁代わりに灰がある（1発で破壊可能）
                                    map[y][x] = TILE.ASH;
                                    blockHp[y][x] = 1;
                                } else {
                                    // 地面は灰色になる
                                    map[y][x] = TILE.ASH_FLOOR;
                                    blockHp[y][x] = 0;
                                }
                            }
                        }
                    }
                }
            }
        }

        // 廃離の森の中心広場（ステージ中央）にメデューサを設置
        let medusa = bosses.find(b => b.id === 'medusa');
        if (medusa) {
            medusa.x = forestX;
            medusa.y = forestY;
            medusa.hp = 350;
            medusa.maxHp = 350;
            medusa.active = true;
            medusa.dead = false;
            medusa.state = 'idle';
            medusa.stateTimer = 500;
            medusa.hitCount = 0;
            medusa.ultReady = false;
            medusa.ultUseCount = 0;
            medusa.burstRemaining = 0;
            medusa.burstTimer = 0;
        }

        // 廃離の森に忘却の木を自然スポーンさせる
        for (let i = 0; i < 15; i++) {
            let angle = Math.random() * Math.PI * 2;
            let r = stageRadius + 3 + Math.random() * (forestRadius - stageRadius - 6);
            let fx = Math.floor(forestX + Math.cos(angle) * r);
            let fy = Math.floor(forestY + Math.sin(angle) * r);
            if (fx > 1 && fx < MAP_W - 2 && fy > 1 && fy < MAP_H - 2 && map[fy][fx] === TILE.ASH_FLOOR) {
                forgottenTrees.push({
                    x: fx,
                    y: fy,
                    hp: 30,
                    maxHp: 30,
                    isNaturalSpawn: true,
                    petrifyTimer: 0,
                    walkPhase: Math.random() * Math.PI * 2
                });
            }
        }
    }
    generateWitheredForest();

    // 深層の土を配置する関数
    function placeDeepDirt(cx, cy, r) {
        for(let y = cy - r; y <= cy + r; y++) {
            for(let x = cx - r; x <= cx + r; x++) {
                if(x >= 0 && x < MAP_W && y >= 0 && y < MAP_H) {
                    if (Math.hypot(x-cx, y-cy) <= r) {
                        map[y][x] = TILE.DEEP_DIRT;
                        blockHp[y][x] = 10;
                    }
                }
            }
        }
    }

    // 南、東、西の石像の周りを「深層の土」で分厚く覆う
    placeDeepDirt(statues[2].x, statues[2].y, 7); // 南
    placeDeepDirt(statues[3].x, statues[3].y, 7); // 東
    placeDeepDirt(statues[4].x, statues[4].y, 7); // 西

    // 切れ目（地割れ）の生成
    function generateChasm(count) {
        for(let i=0; i<count; i++) {
            let cx = Math.floor(Math.random() * MAP_W);
            let cy = Math.floor(Math.random() * MAP_H);
            let length = 5 + Math.floor(Math.random() * 15); // 長さを短く
            let angle = Math.random() * Math.PI * 2;
            
            for(let j=0; j<length; j++) {
                let x = Math.floor(cx);
                let y = Math.floor(cy);
                
                if(x >= 0 && x < MAP_W && y >= 0 && y < MAP_H) {
                    // 水や他の重要施設を極力上書きしないようにする
                    if (map[y][x] === TILE.DIRT || map[y][x] === TILE.FLOOR || map[y][x] === TILE.ROCK || map[y][x] === TILE.IRON) {
                        map[y][x] = TILE.CHASM;
                        blockHp[y][x] = 999;
                    }
                }
                angle += (Math.random() - 0.5) * 0.8; // くねくね曲がる
                cx += Math.cos(angle);
                cy += Math.sin(angle);
            }
        }
    }
    // マップ上に50本程度の切れ目を生成
    generateChasm(50);

    // 重要なオブジェクトの周辺は平地に整地 (深層の土の内側に空洞を作る)
    function clearArea(cx, cy, r, floorTile = TILE.FLOOR) {
        for(let y = cy - r; y <= cy + r; y++) {
            for(let x = cx - r; x <= cx + r; x++) {
                if(x >= 0 && x < MAP_W && y >= 0 && y < MAP_H) {
                    map[y][x] = floorTile;
                    blockHp[y][x] = 0;
                }
            }
        }
    }
    
    // 中央石像のみ整地
    clearArea(statues[0].x, statues[0].y, 3);
    // ボス周りは整地（メデューサの周囲は灰色の地面を保つ）
    bosses.forEach(b => clearArea(b.x, b.y, 6, b.id === 'medusa' ? TILE.ASH_FLOOR : TILE.FLOOR));
    clearArea(finalBoss.x, finalBoss.y, 6);

    // メイズアンレンガで迷路を生成する関数
    function generateMaze(startX, startY, width, height) {
        for(let y=startY; y<startY+height; y++) {
            for(let x=startX; x<startX+width; x++) {
                if(x >= 0 && x < MAP_W && y >= 0 && y < MAP_H) {
                    map[y][x] = TILE.MAZE_BRICK;
                    blockHp[y][x] = 999;
                }
            }
        }
        
        let stack = [{x: 1, y: 1}];
        map[startY+1][startX+1] = TILE.FLOOR;
        let endPoint = {x: 1, y: 1};
        let maxDist = 0;
        
        while(stack.length > 0) {
            let current = stack[stack.length - 1];
            let dirs = [ {dx: 0, dy: -2}, {dx: 0, dy: 2}, {dx: -2, dy: 0}, {dx: 2, dy: 0} ];
            dirs.sort(() => Math.random() - 0.5);
            
            let moved = false;
            for(let d of dirs) {
                let nx = current.x + d.dx;
                let ny = current.y + d.dy;
                if(nx > 0 && nx < width-1 && ny > 0 && ny < height-1) {
                    if(map[startY+ny][startX+nx] === TILE.MAZE_BRICK) {
                        map[startY+ny][startX+nx] = TILE.FLOOR;
                        map[startY+current.y + d.dy/2][startX+current.x + d.dx/2] = TILE.FLOOR;
                        let newDist = (current.dist || 0) + 1;
                        stack.push({x: nx, y: ny, dist: newDist});
                        if (newDist > maxDist) {
                            maxDist = newDist;
                            endPoint = {x: nx, y: ny};
                        }
                        moved = true;
                        break;
                    }
                }
            }
            if(!moved) stack.pop();
        }
        
        // 迷路の中央にボスを配置
        let centerX = startX + Math.floor(width/2);
        let centerY = startY + Math.floor(height/2);
        bosses[0].x = centerX;
        bosses[0].y = centerY;
        clearArea(bosses[0].x, bosses[0].y, 5); // ボス部屋を中央に作る
        
        // 迷路の入り口を開ける
        map[startY+height-1][startX+1] = TILE.FLOOR;
        map[startY+height][startX+1] = TILE.FLOOR;
        clearArea(startX+1, startY+height, 2); // 入り口付近を整地

        // 迷路内にメイズレアンを配置
        for(let i = 0; i < 15; i++) {
            let mx, my;
            let attempts = 0;
            do {
                mx = startX + 2 + Math.floor(Math.random() * (width - 4));
                my = startY + 2 + Math.floor(Math.random() * (height - 4));
                attempts++;
            } while ((map[my][mx] !== TILE.FLOOR || Math.hypot(mx - centerX, my - centerY) < 8) && attempts < 50);

            if (map[my][mx] === TILE.FLOOR) {
                mazerians.push({
                    x: mx, y: my,
                    hp: 40, maxHp: 40,
                    attackCount: 0, warpUsesLeft: 3,
                    state: 'idle', stateTimer: 500 + Math.random() * 1000,
                    vx: 0, vy: 0, facing: 1
                });
            }
        }
    }

    // ランダムな位置に迷路を生成 (幅51, 高さ51)
    let mazeW = 51, mazeH = 51;
    // スタート地点から見て「北側」かつ円形マップ内に収まる位置に配置
    let mazeStartX = Math.max(10, Math.min(MAP_W - mazeW - 10, baseCX - Math.floor(mazeW / 2) + Math.floor((Math.random() - 0.5) * 40)));
    let mazeStartY = Math.max(20, baseCY - mazeH - 65 - Math.floor(Math.random() * 45)); // 開始位置の北65〜110マス付近
    generateMaze(mazeStartX, mazeStartY, mazeW, mazeH);

    // 迷路内にメイズアンリアルの部屋を生成
    function createMazeRoom(cx, cy, r) {
        for(let y = cy-r; y <= cy+r; y++) {
            for(let x = cx-r; x <= cx+r; x++) {
                if(x >= 0 && x < MAP_W && y >= 0 && y < MAP_H) {
                    map[y][x] = TILE.FLOOR;
                    blockHp[y][x] = 0;
                }
            }
        }
        map[cy][cx] = TILE.MAZE_UNREAL;
        blockHp[cy][cx] = 999; // 装置の耐久度 (破壊不能)
        mazeUnreals.push({ x: cx, y: cy, timer: 300000 }); // 5分
    }
    
    // 4つの部屋を迷路内に配置
    createMazeRoom(mazeStartX + 12, mazeStartY + 12, 3);
    createMazeRoom(mazeStartX + mazeW - 12, mazeStartY + 12, 3);
    createMazeRoom(mazeStartX + 12, mazeStartY + mazeH - 12, 3);
    createMazeRoom(mazeStartX + mazeW - 12, mazeStartY + mazeH - 12, 3);

    // 石像を完全に土で埋める (3x3サイズ)
    statues.forEach(s => {
        // 3x3の空間を確保
        for(let oy=-1; oy<=1; oy++) {
            for(let ox=-1; ox<=1; ox++) {
                if(s.type === 'south' || s.type === 'east' || s.type === 'west') {
                    map[s.y+oy][s.x+ox] = TILE.DEEP_DIRT;
                    blockHp[s.y+oy][s.x+ox] = 10;
                } else if (s.type === 'north') {
                    map[s.y+oy][s.x+ox] = TILE.DIRT;
                    blockHp[s.y+oy][s.x+ox] = 3;
                } else if (s.type === 'center') {
                    map[s.y+oy][s.x+ox] = TILE.FLOOR;
                    blockHp[s.y+oy][s.x+ox] = 0;
                }
            }
        }
    });

    // 最初の石像を中心とした円形マップの外側を虚無（VOID）にする
    for (let y = 0; y < MAP_H; y++) {
        for (let x = 0; x < MAP_W; x++) {
            if (Math.hypot(x - baseCX, y - baseCY) > WORLD_RADIUS) {
                map[y][x] = TILE.VOID;
                blockHp[y][x] = 999;
            }
        }
    }
}

// --- ユーティリティ ---
function checkCollision(px, py, size) {
    let r = size / 2;
    let left = Math.floor((px - r) / TS);
    let right = Math.floor((px + r) / TS);
    let top = Math.floor((py - r) / TS);
    let bottom = Math.floor((py + r) / TS);
    
    for(let y = top; y <= bottom; y++) {
        for(let x = left; x <= right; x++) {
            if(x < 0 || x >= MAP_W || y < 0 || y >= MAP_H) return true; // マップ外
            let t = map[y][x];
　　　if(t === TILE.ASH_FLOOR) continue;
            // 通行不可のタイル
            if(t === TILE.VOID || t === TILE.ROCK || t === TILE.IRON || t === TILE.DIRT || t === TILE.ASH || t === TILE.WITHERED_TREE || t === TILE.BOMB || t === TILE.BOMB_ACTIVE || t === TILE.DEEP_DIRT || t === TILE.MAZE_BRICK || t === TILE.ROOT || t === TILE.MAZE_BLOCK || t === TILE.MAZE_UNREAL || (t >= TILE.WORKBENCH && t <= TILE.MEDUSA_CHEST && t !== TILE.WATER && t !== TILE.CHASM && t !== TILE.SWAMP_WATER && t !== TILE.MUD) || t === TILE.WATER || t === TILE.CHASM || t === TILE.SWAMP_WATER || t === TILE.MUD) return true;
        }
    }
    return false;
}

// 視線が通るか判定する関数 (壁越し判定用)
function hasLineOfSight(x0, y0, x1, y1) {
    let dx = Math.abs(x1 - x0);
    let dy = Math.abs(y1 - y0);
    let sx = (x0 < x1) ? 1 : -1;
    let sy = (y0 < y1) ? 1 : -1;
    let err = dx - dy;

    while(true) {
        if(x0 >= 0 && x0 < MAP_W && y0 >= 0 && y0 < MAP_H) {
            let t = map[y0][x0];
            // 障害物なら視線が通らない
            if(t === TILE.VOID || t === TILE.ROCK || t === TILE.IRON || t === TILE.DIRT || t === TILE.ASH || t === TILE.WITHERED_TREE || t === TILE.BOMB || t === TILE.BOMB_ACTIVE || t === TILE.DEEP_DIRT || t === TILE.MAZE_BRICK || t === TILE.ROOT || t === TILE.MAZE_BLOCK || t === TILE.MAZE_UNREAL || (t >= TILE.WORKBENCH && t <= TILE.MEDUSA_CHEST && t !== TILE.WATER && t !== TILE.CHASM && t !== TILE.SWAMP_WATER && t !== TILE.MUD) || t === TILE.MUD) {
                return false;
            }
        }
        if (x0 === x1 && y0 === y1) break;
        let e2 = 2 * err;
        if (e2 > -dy) { err -= dy; x0 += sx; }
        if (e2 < dx) { err += dx; y0 += sy; }
    }
    return true;
}

// プレイヤーリスポーン処理
function respawnPlayer() {
    player.hp = player.maxHp;
    player.px = statues[0].x * TS + TS/2;
    player.py = statues[0].y * TS + TS/2 + TS;
    document.getElementById('gameoverOverlay').classList.add('hidden');
    
    // キーの入力状態をリセット（押しっぱなしバグ防止）
    for (let k in keys) keys[k] = false;
    
    // ボスの状態をリセット（復活直後に突進してくるのを防ぐ）
    bosses.forEach(b => {
        if(b.active && !b.dead) {
            b.state = 'idle';
            b.stateTimer = 1000;
            b.vx = 0;
            b.vy = 0;
        }
    });
    
    player.speedDebuffTimer = 0; // デバフもリセット
    player.petrifyTimer = 0; // 白化の呪いもリセット
    player.stunTimer = 0; // スタンもリセット
    whiteFlashAlpha = 0;
    screenShake = 0;
    isPaused = false;
    updateHUD();
    
    // カメラ位置もリセット
    camX = player.px - canvas.width / 2;
    camY = player.py - canvas.height / 2;
}

// --- 更新処理 ---
function updatePlayer(dt) {
    // 白化の呪い（真っ白＆行動不能）中の処理
    if (player.petrifyTimer > 0) {
        player.petrifyTimer -= dt;
        if (player.petrifyTimer <= 0) {
            player.petrifyTimer = 0;
            spawnParticles(player.px, player.py, '#ffffff');
            spawnDamageText(player.px, player.py - 24, "呪いが解けた！");
        }
        camX = player.px - canvas.width / 2;
        camY = player.py - canvas.height / 2;
        return;
    }

    // スタン（行動不能）中の処理
    if (player.stunTimer > 0) {
        player.stunTimer -= dt;
        if (player.stunTimer < 0) player.stunTimer = 0;
        camX = player.px - canvas.width / 2;
        camY = player.py - canvas.height / 2;
        return;
    }

    let dx = 0, dy = 0;
    if(keys['KeyW'] || keys['ArrowUp']) dy -= 1;
    if(keys['KeyS'] || keys['ArrowDown']) dy += 1;
    if(keys['KeyA'] || keys['ArrowLeft']) dx -= 1;
    if(keys['KeyD'] || keys['ArrowRight']) dx += 1;

    // モバイル用ジョイスティック入力の合算
    if (controlMode === 'mobile' && (mobileJoyVec.x !== 0 || mobileJoyVec.y !== 0)) {
        dx += mobileJoyVec.x;
        dy += mobileJoyVec.y;
    }

    if(dx !== 0 || dy !== 0) {
        // 正規化して斜め移動時の速度上昇を防ぐ
        let len = Math.hypot(dx, dy);
        if (len > 1) {
            dx /= len; dy /= len;
        }

        // 速度デバフの適用
        let currentSpeed = player.speed;
        if (player.speedDebuffTimer > 0) {
            player.speedDebuffTimer -= dt;
            currentSpeed *= 0.2; // 80%ダウン (通常の20%の速度になる)
        }

        player.continuousMoveTime += dt; // ギーラアックス用

        // 向きの更新
        if(Math.abs(dx) > Math.abs(dy)) player.dir = dx > 0 ? 'right' : 'left';
        else player.dir = dy > 0 ? 'down' : 'up';

        // 衝突判定付きの移動 (X, Y 独立して処理して壁滑りを可能に)
        let nPx = player.px + dx * currentSpeed * (dt/(1000/60));
        let nPy = player.py + dy * currentSpeed * (dt/(1000/60));
        let colSize = 18; // プレイヤーの当たり判定サイズ
        
        // 盾装備時は少し遅くなる
        let curItem = player.hotbar[player.selectedSlot];
        if (curItem && ITEM_DATA[curItem] && ITEM_DATA[curItem].type === 'shield') { nPx -= dx * 1.5; nPy -= dy * 1.5; }

        if(isNoclip || !checkCollision(nPx, player.py, colSize)) player.px = nPx;
        if(isNoclip || !checkCollision(player.px, nPy, colSize)) player.py = nPy;
    } else {
        player.continuousMoveTime = 0; // 止まったら火力をリセット
    }

    if (player.medusaEyeCooldown > 0) {
        player.medusaEyeCooldown -= dt;
    }

    // カメラ位置の更新 (プレイヤー中心)
    camX = player.px - canvas.width / 2;
    camY = player.py - canvas.height / 2;
}

function updateExplored() {
    let px = Math.floor(player.px / TS);
    let py = Math.floor(player.py / TS);
    let sight = 8; // 視界の広さ
    
    for(let y = py - sight; y <= py + sight; y++) {
        for(let x = px - sight; x <= px + sight; x++) {
            if(x >= 0 && x < MAP_W && y >= 0 && y < MAP_H) {
                if(Math.hypot(x-px, y-py) <= sight) {
                    explored[y][x] = true;
                }
            }
        }
    }
}

// 装備の耐久度を減らす処理
function reduceDurability(itemId, amount = 1) {
    if (!itemId || !ITEM_DATA[itemId] || !ITEM_DATA[itemId].maxDurability) return;

    // 泥装備は1/2で耐久度が減らない
    if (itemId.includes('mud') && Math.random() < 0.5) return;

    // 灰装備は1/2で即座に壊れる
    if (itemId.includes('ash') && Math.random() < 0.5) {
        breakItem(itemId);
        return;
    }

    if (player.durability[itemId] === undefined) {
        player.durability[itemId] = ITEM_DATA[itemId].maxDurability;
    }
    
    player.durability[itemId] -= amount;

    if (player.durability[itemId] <= 0) {
        breakItem(itemId);
    }
    updateHUD(); // UI更新
}

// アイテムが壊れたときの処理
function breakItem(itemId) {
    if (player.inventory[itemId] > 0) {
        player.inventory[itemId]--;
    }
    if (player.inventory[itemId] <= 0) {
        player.inventory[itemId] = 0;
        // ホットバーからも削除
        for (let i = 0; i < 5; i++) {
            if (player.hotbar[i] === itemId) player.hotbar[i] = null;
        }
    }
    // 次の同名アイテムのために耐久度をリセット
    player.durability[itemId] = ITEM_DATA[itemId].maxDurability;
    spawnDamageText(player.px, player.py - 30, "壊れた!");
}

// 盾の耐久度低下処理
function reduceShieldDurability() {
    let currentItem = player.hotbar[player.selectedSlot];
    if (currentItem && ITEM_DATA[currentItem] && ITEM_DATA[currentItem].type === 'shield') {
        reduceDurability(currentItem);
    }
}

// メデューサ装備による石化反射判定（被弾時）
function checkMedusaPetrifyOnHit(attacker) {
    if (!attacker) return;
    let hasMedusaEquip = false;
    let curItem = player.hotbar[player.selectedSlot];
    if (curItem && ITEM_DATA[curItem] && ITEM_DATA[curItem].petrify && ITEM_DATA[curItem].type === 'shield') {
        hasMedusaEquip = true;
    }
    ['helmet', 'chestplate', 'boots'].forEach(slot => {
        let eqId = player.equipment[slot];
        if (eqId && ITEM_DATA[eqId] && ITEM_DATA[eqId].petrify) {
            hasMedusaEquip = true;
        }
    });
    if (hasMedusaEquip) {
        attacker.petrifyTimer = Math.max(attacker.petrifyTimer || 0, 500);
        let ax = (attacker.x || 0) * TS + TS / 2;
        let ay = (attacker.y || 0) * TS + TS / 2;
        spawnParticles(ax, ay, '#ffffff');
    }
}

function checkPlayerDeath() {
    updateHUD();
    if(player.hp <= 0) {
        isPaused = true;
        document.getElementById('gameoverOverlay').classList.remove('hidden');
    }
}

// 盾のダメージ軽減倍率を取得
function getShieldDamageMultiplier() {
    let itemId = player.hotbar[player.selectedSlot];
    if (itemId && ITEM_DATA[itemId] && ITEM_DATA[itemId].type === 'shield') {
        return 1 - (ITEM_DATA[itemId].reduce || 0);
    }
    return 1.0;
}

// 防具のダメージ軽減倍率を取得
function getArmorDamageMultiplier() {
    let mult = 1.0;
    ['helmet', 'chestplate', 'boots'].forEach(slot => {
        let itemId = player.equipment[slot];
        if (itemId && ITEM_DATA[itemId] && ITEM_DATA[itemId].reduce) {
            mult *= (1 - ITEM_DATA[itemId].reduce);
        }
    });
    return mult;
}

// 防具の耐久度を低下させる
function reduceArmorDurability() {
    ['helmet', 'chestplate', 'boots'].forEach(slot => {
        let itemId = player.equipment[slot];
        if (itemId) {
            reduceDurability(itemId);
        }
    });
}

function updateGoats(dt) {
    for (let i = goats.length - 1; i >= 0; i--) {
        let g = goats[i];
        if (g.hp <= 0) {
            goats.splice(i, 1);
            continue;
        }
        if (g.petrifyTimer > 0) {
            g.petrifyTimer = Math.max(0, g.petrifyTimer - dt);
            continue;
        }
        
        let gx = g.x * TS + TS/2;
        let gy = g.y * TS + TS/2;
        let dist = Math.hypot(player.px - gx, player.py - gy);
        let mapGx = Math.floor(g.x);
        let mapGy = Math.floor(g.y);
        let mapPx = Math.floor(player.px / TS);
        let mapPy = Math.floor(player.py / TS);
        let canSee = hasLineOfSight(mapGx, mapGy, mapPx, mapPy);

        // 敵対解除判定 (10マス以上離れていて、かつ壁越しで見えない場合)
        if (g.hostile) {
            if (dist > TS * 10 && !canSee) {
                g.hostile = false;
                g.attackCount = 0;
                g.state = 'idle';
            }
        }

        g.stateTimer -= dt;
        let angle = Math.atan2(player.py - gy, player.px - gx);

        if (g.state === 'idle' || g.state === 'wander') {
            if (g.hostile) {
                if (g.stateTimer <= 0) {
                    if (dist < TS * 2.5 && canSee) {
                        // 攻撃準備 (3回以上攻撃していれば60%の確率で突撃準備)
                        if (g.attackCount >= 3 && Math.random() < 0.6) {
                            g.state = 'charge_prepare';
                            g.stateTimer = 600;
                        } else {
                            g.state = 'attack_prepare';
                            g.stateTimer = 400;
                        }
                        g.facing = Math.cos(angle) > 0 ? 1 : -1;
                    } else if (dist < TS * 12 && canSee) {
                        // 遠くて必殺技が溜まっているなら突撃
                        if (g.attackCount >= 3 && Math.random() < 0.6) {
                            g.state = 'charge_prepare';
                            g.stateTimer = 600;
                            g.facing = Math.cos(angle) > 0 ? 1 : -1;
                        } else {
                            // 追いかける
                            g.state = 'chase';
                            g.stateTimer = 300;
                        }
                    } else {
                        // 見失ってるか遠い
                        g.state = 'wander';
                        g.stateTimer = 500 + Math.random()*500;
                        g.facing = Math.random() > 0.5 ? 1 : -1;
                        g.vx = (Math.random() - 0.5) * 2;
                        g.vy = (Math.random() - 0.5) * 2;
                    }
                }
            } else {
                // 中立時の行動
                if (g.stateTimer <= 0) {
                    if (Math.random() < 0.4) {
                        g.state = 'wander';
                        g.stateTimer = 1000 + Math.random()*1000;
                        g.facing = Math.random() > 0.5 ? 1 : -1;
                        let a = Math.random() * Math.PI * 2;
                        g.vx = Math.cos(a) * 1;
                        g.vy = Math.sin(a) * 1;
                    } else {
                        g.state = 'idle';
                        g.stateTimer = 1000;
                    }
                }
            }
        }
        
        if (g.state === 'wander') {
            g.x += g.vx * (dt/16) / TS;
            g.y += g.vy * (dt/16) / TS;
            if (checkCollision(g.x * TS + TS/2, g.y * TS + TS/2, 16)) {
                g.x -= g.vx * (dt/16) / TS;
                g.y -= g.vy * (dt/16) / TS;
                g.state = 'idle';
            }
        }

        if (g.state === 'chase') {
            g.facing = Math.cos(angle) > 0 ? 1 : -1;
            let speed = 3; 
            g.x += (Math.cos(angle) * speed * (dt/16)) / TS;
            g.y += (Math.sin(angle) * speed * (dt/16)) / TS;
            if (g.stateTimer <= 0) g.state = 'idle';
        }

        if (g.state === 'attack_prepare') {
            if (g.stateTimer <= 0) {
                g.state = 'attack';
                g.stateTimer = 200;
                g.attackCount++; // 攻撃回数をカウント
                
                if (dist < TS * 3 && canSee) {
                    let dmgMult = getShieldDamageMultiplier() * getArmorDamageMultiplier();
                    player.hp -= 15 * dmgMult; // 角攻撃
                    reduceShieldDurability(); // 盾の耐久減少
                    reduceArmorDurability(); // 防具の耐久減少
                    checkMedusaPetrifyOnHit(g);
                    spawnDamageText(player.px, player.py - 20, Math.round(15 * dmgMult));
                    spawnParticles(player.px, player.py, '#e74c3c');
                    screenShake = 5;
                    checkPlayerDeath();
                }
            }
        }

        if (g.state === 'attack') {
            if (g.stateTimer <= 0) g.state = 'idle';
        }

        if (g.state === 'charge_prepare') {
            if (g.stateTimer <= 0) {
                g.state = 'charge';
                g.stateTimer = 1000; // 突撃時間
                g.vx = Math.cos(angle) * 7; 
                g.vy = Math.sin(angle) * 7;
                g.attackCount = 0; // 必殺技を使ったのでリセット
            }
        }

        if (g.state === 'charge') {
            let nx = g.x * TS + TS/2 + g.vx * (dt/16);
            let ny = g.y * TS + TS/2 + g.vy * (dt/16);

            // 壁破壊判定
            let tGx = Math.floor(nx / TS);
            let tGy = Math.floor(ny / TS);
            if (tGx >= 0 && tGx < MAP_W && tGy >= 0 && tGy < MAP_H) {
                let t = map[tGy][tGx];
                // 破壊可能な壁なら壊しながら進む
                if (t === TILE.ROCK || t === TILE.IRON || t === TILE.DIRT || t === TILE.ASH || t === TILE.WITHERED_TREE || t === TILE.BOMB || t === TILE.DEEP_DIRT || t === TILE.ROOT) {
                    map[tGy][tGx] = TILE.FLOOR;
                    blockHp[tGy][tGx] = 0;
                    spawnParticles(nx, ny, '#bdc3c7');
                } else if (t === TILE.MAZE_BRICK || t === TILE.WATER || t === TILE.MAZE_UNREAL) {
                    // 破壊不能なものにぶつかったらスタン
                    g.state = 'stun';
                    g.stateTimer = 1500;
                    screenShake = 8;
                }
            }

            if (g.state === 'charge') { // スタンしていなければ進む
                g.x = (nx - TS/2) / TS;
                g.y = (ny - TS/2) / TS;
                let currentDist = Math.hypot(player.px - (g.x*TS+TS/2), player.py - (g.y*TS+TS/2));
                if (currentDist < TS * 2) {
                    let dmgMult = getShieldDamageMultiplier() * getArmorDamageMultiplier();
                    player.hp -= 35 * dmgMult; // 突進ダメージ
                    reduceShieldDurability(); // 盾の耐久減少
                    reduceArmorDurability(); // 防具の耐久減少
                    checkMedusaPetrifyOnHit(g);
                    spawnDamageText(player.px, player.py - 20, Math.round(35 * dmgMult));
                    spawnParticles(player.px, player.py, '#e74c3c');
                    screenShake = 12;
                    g.state = 'stun';
                    g.stateTimer = 1000;
                    
                    let kbAngle = Math.atan2(player.py - gy, player.px - gx);
                    player.px += Math.cos(kbAngle) * 30; 
                    player.py += Math.sin(kbAngle) * 30;
                    checkPlayerDeath();
                }
            }

            if (g.stateTimer <= 0 && g.state === 'charge') g.state = 'idle';
        }

        if (g.state === 'stun') {
            if (g.stateTimer <= 0) g.state = 'idle';
        }
    }
}

function updateBulls(dt) {
    // 洞窟（FLOOR）での自然スポーン処理
    if (bulls.length < 35 && Math.random() < 0.015) {
        let angle = Math.random() * Math.PI * 2;
        let dist = TS * (10 + Math.random() * 14);
        let sx = Math.floor((player.px + Math.cos(angle) * dist) / TS);
        let sy = Math.floor((player.py + Math.sin(angle) * dist) / TS);
        if (sx > 2 && sx < MAP_W - 3 && sy > 2 && sy < MAP_H - 3 && map[sy][sx] === TILE.FLOOR) {
            if (Math.hypot(sx - statues[0].x, sy - statues[0].y) > 15) {
                bulls.push({
                    x: sx,
                    y: sy,
                    hp: 180,
                    maxHp: 180,
                    state: 'idle',
                    stateTimer: 500,
                    chargeReady: true, // スポーン時から必殺技（突撃）が使用可能
                    sweepHits: 0,      // 通常攻撃のヒット回数カウンター
                    vx: 0,
                    vy: 0,
                    facing: 1
                });
            }
        }
    }

    for (let i = bulls.length - 1; i >= 0; i--) {
        let b = bulls[i];
        if (b.hp <= 0) {
            bulls.splice(i, 1);
            continue;
        }
        if (b.petrifyTimer > 0) {
            b.petrifyTimer = Math.max(0, b.petrifyTimer - dt);
            continue;
        }

        // 初期スポーン時や既存個体用の安全初期化
        if (b.chargeReady === undefined) b.chargeReady = true;
        if (b.sweepHits === undefined) b.sweepHits = 0;

        let bx = b.x * TS + TS / 2;
        let by = b.y * TS + TS / 2;
        let dist = Math.hypot(player.px - bx, player.py - by);
        let canSee = hasLineOfSight(Math.floor(b.x), Math.floor(b.y), Math.floor(player.px / TS), Math.floor(player.py / TS));
        let angle = Math.atan2(player.py - by, player.px - bx);

        b.stateTimer -= dt;

        if (b.state === 'idle' || b.state === 'wander') {
            if (dist < TS * 9 && canSee) {
                b.facing = Math.cos(angle) > 0 ? 1 : -1;
                if (dist < TS * 2.3) {
                    // 通常攻撃：ツノはらい（ダメージ6＋ノックバック）
                    b.state = 'sweep_prepare';
                    b.stateTimer = 350;
                } else if (dist < TS * 6.5 && b.chargeReady) {
                    // 必殺技：前方突撃準備（避けられるよう速度を 7.5 → 4.2 に低下）
                    b.state = 'charge_prepare';
                    b.stateTimer = 650;
                    b.vx = Math.cos(angle) * 4.2;
                    b.vy = Math.sin(angle) * 4.2;
                } else {
                    b.state = 'chase';
                    b.stateTimer = 250;
                }
            } else if (b.stateTimer <= 0) {
                if (Math.random() < 0.45) {
                    b.state = 'wander';
                    b.stateTimer = 1000 + Math.random() * 1000;
                    let a = Math.random() * Math.PI * 2;
                    b.facing = Math.cos(a) > 0 ? 1 : -1;
                    b.vx = Math.cos(a) * 1.2;
                    b.vy = Math.sin(a) * 1.2;
                } else {
                    b.state = 'idle';
                    b.stateTimer = 1000;
                }
            }
        }

        if (b.state === 'wander') {
            let nx = bx + b.vx * (dt / 16);
            let ny = by + b.vy * (dt / 16);
            if (!checkCollision(nx, ny, 20)) {
                b.x = (nx - TS / 2) / TS;
                b.y = (ny - TS / 2) / TS;
            } else {
                b.state = 'idle';
                b.stateTimer = 400;
            }
        } else if (b.state === 'chase') {
            b.facing = Math.cos(angle) > 0 ? 1 : -1;
            let speed = 2.6;
            let nx = bx + Math.cos(angle) * speed * (dt / 16);
            let ny = by + Math.sin(angle) * speed * (dt / 16);
            if (!checkCollision(nx, by, 20)) b.x = (nx - TS / 2) / TS;
            if (!checkCollision(bx, ny, 20)) b.y = (ny - TS / 2) / TS;
            if (b.stateTimer <= 0) b.state = 'idle';
        } else if (b.state === 'sweep_prepare') {
            b.facing = Math.cos(angle) > 0 ? 1 : -1;
            if (b.stateTimer <= 0) {
                b.state = 'sweep';
                b.stateTimer = 220;
                let curDist = Math.hypot(player.px - bx, player.py - by);
                if (curDist < TS * 2.8 && canSee) {
                    let dmgMult = getShieldDamageMultiplier() * getArmorDamageMultiplier();
                    let dmg = Math.round(6 * dmgMult); // ツノ払いの基本火力を 18 → 6 に弱体化
                    player.hp -= dmg;
                    reduceShieldDurability();
                    reduceArmorDurability();
                    checkMedusaPetrifyOnHit(b);
                    spawnDamageText(player.px, player.py - 20, dmg);
                    spawnParticles(player.px, player.py, '#f59e0b');
                    screenShake = 6;

                    // 通常攻撃のヒットカウント＆必殺技チャージ判定（4〜8発で溜まり、5発目以降は50%の確率で使用可能）
                    if (!b.chargeReady) {
                        b.sweepHits++;
                        if (b.sweepHits >= 8) {
                            b.chargeReady = true;
                        } else if (b.sweepHits >= 5 && Math.random() < 0.5) {
                            b.chargeReady = true;
                        } else if (b.sweepHits === 4 && Math.random() < 0.25) {
                            b.chargeReady = true;
                        }
                    }

                    // ツノはらいで距離を離す（強めのノックバック＋ブル自身も少し後退）
                    let kbAngle = Math.atan2(player.py - by, player.px - bx);
                    for (let step = 0; step < 5; step++) {
                        let kx = player.px + Math.cos(kbAngle) * 14;
                        let ky = player.py + Math.sin(kbAngle) * 14;
                        if (!checkCollision(kx, player.py, 18)) player.px = kx;
                        if (!checkCollision(player.px, ky, 18)) player.py = ky;
                    }
                    let backX = bx - Math.cos(kbAngle) * 20;
                    let backY = by - Math.sin(kbAngle) * 20;
                    if (!checkCollision(backX, by, 20)) b.x = (backX - TS / 2) / TS;
                    if (!checkCollision(bx, backY, 20)) b.y = (backY - TS / 2) / TS;

                    checkPlayerDeath();
                }
            }
        } else if (b.state === 'sweep') {
            if (b.stateTimer <= 0) {
                b.state = 'idle';
                b.stateTimer = 500;
            }
        } else if (b.state === 'charge_prepare') {
            b.facing = Math.cos(angle) > 0 ? 1 : -1;
            b.vx = Math.cos(angle) * 4.2; // 突撃速度を 7.5 → 4.2 に低下
            b.vy = Math.sin(angle) * 4.2;
            if (b.stateTimer <= 0) {
                b.state = 'charge';
                b.stateTimer = 950;   // 速度を落とした分、移動時間を少し調整
                b.chargeReady = false; // 必殺技を消費し、ヒットカウントをリセット
                b.sweepHits = 0;
            }
        } else if (b.state === 'charge') {
            let nx = bx + b.vx * (dt / 16);
            let ny = by + b.vy * (dt / 16);
            if (checkCollision(nx, ny, 20)) {
                b.state = 'stun';
                b.stateTimer = 900;
                screenShake = 7;
            } else {
                b.x = (nx - TS / 2) / TS;
                b.y = (ny - TS / 2) / TS;
                let curDist = Math.hypot(player.px - nx, player.py - ny);
                if (curDist < TS * 1.4) {
                    let dmgMult = getShieldDamageMultiplier() * getArmorDamageMultiplier();
                    let dmg = Math.round(24 * dmgMult);
                    player.hp -= dmg;
                    player.stunTimer = Math.max(player.stunTimer || 0, 500); // 0.5秒スタン
                    reduceShieldDurability();
                    reduceArmorDurability();
                    checkMedusaPetrifyOnHit(b);
                    spawnDamageText(player.px, player.py - 22, dmg); // 頭上の「(スタン!)」表記を削除し数値のみに変更
                    spawnParticles(player.px, player.py, '#facc15');
                    screenShake = 10;

                    let kbAngle = Math.atan2(player.py - ny, player.px - nx);
                    let kx = player.px + Math.cos(kbAngle) * 22;
                    let ky = player.py + Math.sin(kbAngle) * 22;
                    if (!checkCollision(kx, player.py, 18)) player.px = kx;
                    if (!checkCollision(player.px, ky, 18)) player.py = ky;

                    b.state = 'idle';
                    b.stateTimer = 650;
                    checkPlayerDeath();
                }
            }
            if (b.state === 'charge' && b.stateTimer <= 0) {
                b.state = 'idle';
                b.stateTimer = 500;
            }
        } else if (b.state === 'stun') {
            if (b.stateTimer <= 0) b.state = 'idle';
        }
    }
}

function updateFrogs(dt) {
    for (let i = frogs.length - 1; i >= 0; i--) {
        let f = frogs[i];
        if (f.hp <= 0) {
            frogs.splice(i, 1);
            continue;
        }
        if (f.petrifyTimer > 0) {
            f.petrifyTimer = Math.max(0, f.petrifyTimer - dt);
            continue;
        }
        
        let fx = f.x * TS + TS/2;
        let fy = f.y * TS + TS/2;
        let dist = Math.hypot(player.px - fx, player.py - fy);
        let mapFx = Math.floor(f.x);
        let mapFy = Math.floor(f.y);
        let mapPx = Math.floor(player.px / TS);
        let mapPy = Math.floor(player.py / TS);
        let canSee = hasLineOfSight(mapFx, mapFy, mapPx, mapPy);

        // 敵対解除判定 (10マス以上離れて見えない場合)
        if (f.hostile) {
            if (dist > TS * 10 && !canSee) {
                f.hostile = false;
                f.attackCount = 0;
                f.state = 'idle';
            }
        }

        f.stateTimer -= dt;
        let angle = Math.atan2(player.py - fy, player.px - fx);

        if (f.state === 'idle') {
            if (f.stateTimer <= 0) {
                if (f.hostile) {
                    if (dist < TS * 12 && canSee) {
                        f.facing = Math.cos(angle) > 0 ? 1 : -1;
                        if (f.attackCount >= 3 && Math.random() < 0.6) {
                            // 必殺技（粘液）準備
                            f.state = 'spit_prepare';
                            f.stateTimer = 500;
                        } else {
                            // ジャンプ攻撃準備
                            f.state = 'jump_prepare';
                            f.stateTimer = 400;
                            // ターゲット設定（最大5マス先まで）
                            let jumpDist = Math.min(dist, TS * 5); 
                            f.jumpStartX = f.x;
                            f.jumpStartY = f.y;
                            f.jumpEndX = f.x + Math.cos(angle) * jumpDist / TS;
                            f.jumpEndY = f.y + Math.sin(angle) * jumpDist / TS;
                        }
                    } else {
                        // 見失った場合、ランダムにジャンプ
                        f.state = 'jump_prepare';
                        f.stateTimer = 300;
                        let a = Math.random() * Math.PI * 2;
                        f.facing = Math.cos(a) > 0 ? 1 : -1;
                        let jumpDist = TS * 2;
                        f.jumpStartX = f.x; f.jumpStartY = f.y;
                        f.jumpEndX = f.x + Math.cos(a) * jumpDist / TS;
                        f.jumpEndY = f.y + Math.sin(a) * jumpDist / TS;
                    }
                } else {
                    // 中立のウロウロ
                    if (Math.random() < 0.3) {
                        f.state = 'jump_prepare';
                        f.stateTimer = 500;
                        let a = Math.random() * Math.PI * 2;
                        f.facing = Math.cos(a) > 0 ? 1 : -1;
                        let jumpDist = TS * 1.5;
                        f.jumpStartX = f.x; f.jumpStartY = f.y;
                        f.jumpEndX = f.x + Math.cos(a) * jumpDist / TS;
                        f.jumpEndY = f.y + Math.sin(a) * jumpDist / TS;
                    } else {
                        f.state = 'idle';
                        f.stateTimer = 1000 + Math.random()*1000;
                    }
                }
            }
        }
        
        if (f.state === 'jump_prepare') {
            if (f.stateTimer <= 0) {
                f.state = 'jump';
                f.stateTimer = 500; // ジャンプの滞空時間
                f.jumpMaxTime = 500;
            }
        }
        
        if (f.state === 'jump') {
            let progress = 1 - (f.stateTimer / f.jumpMaxTime);
            // 放物線を描くように移動 (壁や水を飛び越えるため、移動中の当たり判定は無視)
            f.x = f.jumpStartX + (f.jumpEndX - f.jumpStartX) * progress;
            f.y = f.jumpStartY + (f.jumpEndY - f.jumpStartY) * progress;
            f.z = Math.sin(progress * Math.PI) * 40; // アーチの高さ
            
            if (f.stateTimer <= 0) {
                f.state = 'idle';
                f.stateTimer = 500;
                f.z = 0;
                
                // 着地点が壁なら元の位置に戻る（ジャンプ失敗）
                if (checkCollision(f.x*TS+TS/2, f.y*TS+TS/2, 16)) {
                    f.x = f.jumpStartX;
                    f.y = f.jumpStartY;
                }
                
                // 着地時にプレイヤーが近くにいればダメージ
                if (f.hostile) {
                    let pDist = Math.hypot(player.px - (f.x*TS+TS/2), player.py - (f.y*TS+TS/2));
                    if (pDist < TS * 1.5) {
                        let dmgMult = getShieldDamageMultiplier() * getArmorDamageMultiplier();
                        player.hp -= 20 * dmgMult;
                        reduceShieldDurability(); // 盾の耐久減少
                        reduceArmorDurability(); // 防具の耐久減少
                        checkMedusaPetrifyOnHit(f);
                        spawnDamageText(player.px, player.py - 20, Math.round(20 * dmgMult));
                        screenShake = 6;
                        checkPlayerDeath();
                    }
                    f.attackCount++; // 攻撃（ジャンプ）回数増加
                }
            }
        }
        
        if (f.state === 'spit_prepare') {
            if (f.stateTimer <= 0) {
                f.state = 'idle';
                f.stateTimer = 1000;
                f.attackCount = 0; // 必殺技を使ったのでリセット
                
                // 粘液発射
                projectiles.push({
                    x: f.x * TS + TS/2,
                    y: f.y * TS + TS/2,
                    vx: Math.cos(angle) * 7,
                    vy: Math.sin(angle) * 7,
                    type: 'slime',
                    life: 2000 // 2秒で消滅
                });
            }
        }
    }
}

function updateMazerians(dt) {
    for (let i = mazerians.length - 1; i >= 0; i--) {
        let m = mazerians[i];
        if (m.hp <= 0) {
            mazerians.splice(i, 1);
            continue;
        }
        if (m.petrifyTimer > 0) {
            m.petrifyTimer = Math.max(0, m.petrifyTimer - dt);
            continue;
        }

        let mx = m.x * TS + TS/2;
        let my = m.y * TS + TS/2;
        let dist = Math.hypot(player.px - mx, player.py - my);
        let mapMx = Math.floor(m.x);
        let mapMy = Math.floor(m.y);
        let mapPx = Math.floor(player.px / TS);
        let mapPy = Math.floor(player.py / TS);
        let canSee = hasLineOfSight(mapMx, mapMy, mapPx, mapPy);

        m.stateTimer -= dt;
        let angle = Math.atan2(player.py - my, player.px - mx);

        if (m.state === 'idle') {
            if (m.stateTimer <= 0) {
                if (dist < TS * 10 && canSee) {
                    m.facing = Math.cos(angle) > 0 ? 1 : -1;
                    if (Math.random() < 0.6) {
                        m.state = 'shoot_prepare';
                        m.stateTimer = 600;
                    } else {
                        m.state = 'wander';
                        m.stateTimer = 800;
                        let a = angle + (Math.random() - 0.5) * 1.5;
                        m.vx = Math.cos(a) * 1.5;
                        m.vy = Math.sin(a) * 1.5;
                    }
                } else {
                    m.state = 'wander';
                    m.stateTimer = 1000 + Math.random() * 1000;
                    let a = Math.random() * Math.PI * 2;
                    m.facing = Math.cos(a) > 0 ? 1 : -1;
                    m.vx = Math.cos(a) * 1.2;
                    m.vy = Math.sin(a) * 1.2;
                }
            }
        } else if (m.state === 'wander') {
            let nx = mx + m.vx * (dt/16);
            let ny = my + m.vy * (dt/16);
            if (!checkCollision(nx, ny, 16)) {
                m.x = (nx - TS/2) / TS;
                m.y = (ny - TS/2) / TS;
            } else {
                m.state = 'idle';
                m.stateTimer = 300;
            }
            if (m.stateTimer <= 0) {
                m.state = 'idle';
                m.stateTimer = 400;
            }
        } else if (m.state === 'shoot_prepare') {
            m.facing = Math.cos(angle) > 0 ? 1 : -1;
            if (m.stateTimer <= 0) {
                m.state = 'idle';
                m.stateTimer = 1400;
                m.attackCount = (m.attackCount || 0) + 1;

                let speed = 2.2;
                let leftAngle = angle - Math.PI / 2;
                let rightAngle = angle + Math.PI / 2;

                projectiles.push({
                    x: mx, y: my,
                    vx: Math.cos(leftAngle) * speed,
                    vy: Math.sin(leftAngle) * speed,
                    type: 'mazerian_homing',
                    life: 5000
                });
                projectiles.push({
                    x: mx, y: my,
                    vx: Math.cos(rightAngle) * speed,
                    vy: Math.sin(rightAngle) * speed,
                    type: 'mazerian_homing',
                    life: 5000
                });
            }
        }
    }
}

// メデューサの必殺技：周囲に大量の木を生やす＋2回に1回全員を3秒間真っ白＆行動不能＋3回目で周囲の木が忘却の木になる
function triggerMedusaUltimate(medusa) {
    let mx = medusa.x * TS + TS / 2;
    let my = medusa.y * TS + TS / 2;
    medusa.ultUseCount = (medusa.ultUseCount || 0) + 1;
    screenShake = 12;

    let pCol = Math.floor(player.px / TS);
    let pRow = Math.floor(player.py / TS);
    let mCol = Math.floor(medusa.x);
    let mRow = Math.floor(medusa.y);

    // プレイヤーとメデューサの周囲の候補タイルを収集して大量の木を生やす
    let candidates = [];
    for (let dy = -7; dy <= 7; dy++) {
        for (let dx = -7; dx <= 7; dx++) {
            let tx = pCol + dx;
            let ty = pRow + dy;
            if (tx <= 1 || tx >= MAP_W - 2 || ty <= 1 || ty >= MAP_H - 2) continue;
            if (tx === pCol && ty === pRow) continue;
            if (Math.abs(tx - mCol) <= 1 && Math.abs(ty - mRow) <= 1) continue;

            let t = map[ty][tx];
            if (t === TILE.FLOOR || t === TILE.ASH_FLOOR || t === TILE.HAY) {
                candidates.push({ x: tx, y: ty });
            }
        }
    }

    candidates.sort(() => Math.random() - 0.5);
    let treeCount = Math.min(22, candidates.length);
    for (let i = 0; i < treeCount; i++) {
        let pos = candidates[i];
        map[pos.y][pos.x] = TILE.WITHERED_TREE;
        blockHp[pos.y][pos.x] = 3;
        spawnParticles(pos.x * TS + TS / 2, pos.y * TS + TS / 2, '#2ecc71');
    }

    // 必殺技3回目（および3の倍数回目）：周りにある木が「忘却の木」という敵になる
    if (medusa.ultUseCount % 3 === 0) {
        spawnDamageText(mx, my - 55, "必殺技3回目：忘却の木の覚醒！");
        let awakenRadius = 18;
        for (let ty = Math.max(1, mRow - awakenRadius); ty <= Math.min(MAP_H - 2, mRow + awakenRadius); ty++) {
            for (let tx = Math.max(1, mCol - awakenRadius); tx <= Math.min(MAP_W - 2, mCol + awakenRadius); tx++) {
                if (map[ty][tx] === TILE.WITHERED_TREE || map[ty][tx] === TILE.ROOT) {
                    map[ty][tx] = TILE.ASH_FLOOR;
                    blockHp[ty][tx] = 0;
                    forgottenTrees.push({
                        x: tx,
                        y: ty,
                        hp: 30,
                        maxHp: 30,
                        lifeTimer: 5000,
                        petrifyTimer: 0,
                        walkPhase: Math.random() * Math.PI * 2
                    });
                    spawnParticles(tx * TS + TS / 2, ty * TS + TS / 2, '#cbd5e1');
                }
            }
        }
    }

    // 2回に1回（50%）の確率で周りにいる者全員を真っ白にして3秒間行動不能にする
    if (Math.random() < 0.5) {
        whiteFlashAlpha = 0.85;
        spawnDamageText(mx, my - 35, "白化の呪縛！（3秒間行動不能）");
        let curseRadius = TS * 10;

        // プレイヤーが範囲内にいれば真っ白＆3秒間行動不能
        if (Math.hypot(player.px - mx, player.py - my) <= curseRadius) {
            player.petrifyTimer = 3000;
            spawnParticles(player.px, player.py, '#ffffff');
        }

        // 周囲のモンスター全員も真っ白＆3秒間行動不能
        goats.forEach(g => {
            if (Math.hypot(g.x * TS + TS / 2 - mx, g.y * TS + TS / 2 - my) <= curseRadius) g.petrifyTimer = 3000;
        });
        bulls.forEach(b => {
            if (Math.hypot(b.x * TS + TS / 2 - mx, b.y * TS + TS / 2 - my) <= curseRadius) b.petrifyTimer = 3000;
        });
        frogs.forEach(f => {
            if (Math.hypot(f.x * TS + TS / 2 - mx, f.y * TS + TS / 2 - my) <= curseRadius) f.petrifyTimer = 3000;
        });
        mazerians.forEach(m => {
            if (Math.hypot(m.x * TS + TS / 2 - mx, m.y * TS + TS / 2 - my) <= curseRadius) m.petrifyTimer = 3000;
        });
        forgottenTrees.forEach(ft => {
            if (Math.hypot(ft.x * TS + TS / 2 - mx, ft.y * TS + TS / 2 - my) <= curseRadius) ft.petrifyTimer = 3000;
        });
    } else {
        spawnDamageText(mx, my - 35, "必殺技：森羅の呪縛！");
    }
}

// 忘却の木のドロップ処理
function dropForgottenTreeReward(fx, fy) {
    addItemOrDrop('withered_wood', 1, fx, fy);
    updateHUD();
}

// 忘却の木の更新処理（プレイヤーを追跡し、火力1〜2のダメージを与えた瞬間に死ぬ／5秒経つと普通の木に戻る）
function updateForgottenTrees(dt) {
    // 廃離の森での自然スポーン処理
    if (witheredForestInfo) {
        witheredForestInfo.spawnTimer -= dt;
        if (witheredForestInfo.spawnTimer <= 0) {
            witheredForestInfo.spawnTimer = 8000;
            let naturalCount = forgottenTrees.filter(ft => ft.isNaturalSpawn).length;
            if (naturalCount < 15) {
                for (let attempt = 0; attempt < 10; attempt++) {
                    let angle = Math.random() * Math.PI * 2;
                    let r = 16 + Math.random() * (witheredForestInfo.radius - 20);
                    let sx = Math.floor(witheredForestInfo.x + Math.cos(angle) * r);
                    let sy = Math.floor(witheredForestInfo.y + Math.sin(angle) * r);
                    if (sx > 1 && sx < MAP_W - 2 && sy > 1 && sy < MAP_H - 2 && map[sy][sx] === TILE.ASH_FLOOR) {
                        let distToPlayer = Math.hypot(player.px - (sx * TS + TS / 2), player.py - (sy * TS + TS / 2));
                        if (distToPlayer > TS * 5) {
                            forgottenTrees.push({
                                x: sx,
                                y: sy,
                                hp: 30,
                                maxHp: 30,
                                isNaturalSpawn: true,
                                petrifyTimer: 0,
                                walkPhase: Math.random() * Math.PI * 2
                            });
                            break;
                        }
                    }
                }
            }
        }
    }

    for (let i = forgottenTrees.length - 1; i >= 0; i--) {
        let ft = forgottenTrees[i];
        if (ft.hp <= 0) {
            forgottenTrees.splice(i, 1);
            continue;
        }

        // メデューサの必殺技で生まれた個体のみ、5秒経過で普通の木に戻る
        if (!ft.isNaturalSpawn) {
            ft.lifeTimer = (ft.lifeTimer !== undefined ? ft.lifeTimer : 5000) - dt;
            if (ft.lifeTimer <= 0) {
                let tx = Math.max(1, Math.min(MAP_W - 2, Math.round(ft.x)));
                let ty = Math.max(1, Math.min(MAP_H - 2, Math.round(ft.y)));
                let pCol = Math.floor(player.px / TS);
                let pRow = Math.floor(player.py / TS);

                // プレイヤーと重なっている場合は近くの空きマスを探す
                if (tx === pCol && ty === pRow) {
                    const offsets = [[1,0],[-1,0],[0,1],[0,-1],[1,1],[-1,1],[1,-1],[-1,-1]];
                    for (let [ox, oy] of offsets) {
                        let nx = tx + ox, ny = ty + oy;
                        if (nx > 0 && nx < MAP_W - 1 && ny > 0 && ny < MAP_H - 1 && (map[ny][nx] === TILE.FLOOR || map[ny][nx] === TILE.ASH_FLOOR)) {
                            tx = nx; ty = ny;
                            break;
                        }
                    }
                }

                if (map[ty][tx] === TILE.FLOOR || map[ty][tx] === TILE.ASH_FLOOR || map[ty][tx] === TILE.HAY) {
                    map[ty][tx] = TILE.WITHERED_TREE;
                    blockHp[ty][tx] = 3;
                }
                spawnParticles(tx * TS + TS / 2, ty * TS + TS / 2, '#94a3b8');
                spawnDamageText(tx * TS + TS / 2, ty * TS - 12, "木に戻った");
                forgottenTrees.splice(i, 1);
                continue;
            }
        }

        if (ft.petrifyTimer > 0) {
            ft.petrifyTimer = Math.max(0, ft.petrifyTimer - dt);
            continue;
        }

        let fx = ft.x * TS + TS / 2;
        let fy = ft.y * TS + TS / 2;
        let dist = Math.hypot(player.px - fx, player.py - fy);

        if (dist > 4 && (!ft.isNaturalSpawn || dist < TS * 14)) {
            ft.walkPhase = (ft.walkPhase || 0) + dt * 0.01;
            let angle = Math.atan2(player.py - fy, player.px - fx);
            let speed = 2.3;
            let nx = fx + Math.cos(angle) * speed * (dt / 16);
            let ny = fy + Math.sin(angle) * speed * (dt / 16);

            if (!checkCollision(nx, fy, 18)) fx = nx;
            if (!checkCollision(fx, ny, 18)) fy = ny;

            ft.x = (fx - TS / 2) / TS;
            ft.y = (fy - TS / 2) / TS;
        }

        // プレイヤーに接触してダメージを与えた瞬間に死ぬ（火力1〜2）
        if (Math.hypot(player.px - fx, player.py - fy) < TS * 0.95) {
            let dmg = Math.random() < 0.5 ? 1 : 2;
            let dmgMult = getShieldDamageMultiplier() * getArmorDamageMultiplier();
            player.hp -= Math.max(1, Math.round(dmg * dmgMult));
            reduceArmorDurability();
            spawnDamageText(player.px, player.py - 22, Math.max(1, Math.round(dmg * dmgMult)));
            spawnParticles(fx, fy, '#cbd5e1');
            spawnParticles(fx, fy, '#475569');
            screenShake = 4;

            // ダメージを与えた瞬間自滅して死ぬ
            ft.hp = 0;
            addItemOrDrop('withered_wood', 1, fx, fy);
            updateHUD();
            forgottenTrees.splice(i, 1);
            checkPlayerDeath();
        }
    }
}

function updateProjectiles(dt) {
    for (let i = projectiles.length - 1; i >= 0; i--) {
        let p = projectiles[i];

        // ホーミング弾の誘導処理
        if (p.type === 'mazerian_homing') {
            let targetAngle = Math.atan2(player.py - p.y, player.px - p.x);
            let currentAngle = Math.atan2(p.vy, p.vx);
            let speed = Math.hypot(p.vx, p.vy);

            let diff = targetAngle - currentAngle;
            while (diff > Math.PI) diff -= Math.PI * 2;
            while (diff < -Math.PI) diff += Math.PI * 2;

            // 徐々にプレイヤーの方へ旋回する
            let turnSpeed = 0.032 * (dt / 16);
            currentAngle += Math.sign(diff) * Math.min(Math.abs(diff), turnSpeed);

            p.vx = Math.cos(currentAngle) * speed;
            p.vy = Math.sin(currentAngle) * speed;
        }

        p.x += p.vx * (dt/16);
        p.y += p.vy * (dt/16);
        p.life -= dt;
        
        // 障害物に当たったら消滅（メイズレアンの弾は壁を貫通する）
        if (p.type !== 'mazerian_homing' && checkCollision(p.x, p.y, 6)) {
            spawnParticles(p.x, p.y, '#2ecc71');
            projectiles.splice(i, 1);
            continue;
        }
        
        // プレイヤーへの当たり判定
        if (Math.hypot(player.px - p.x, player.py - p.y) < TS * 0.75) {
            let dmgMult = getShieldDamageMultiplier() * getArmorDamageMultiplier();
            if (p.type === 'slime') {
                player.hp -= 10 * dmgMult;
                reduceShieldDurability(); // 盾の耐久減少
                reduceArmorDurability();
                player.speedDebuffTimer = 5000; // 5秒間速度デバフ
                spawnDamageText(player.px, player.py - 20, "速度低下!");
                spawnParticles(player.px, player.py, '#2ecc71');
                screenShake = 3;
                checkPlayerDeath();
            } else if (p.type === 'mazerian_homing') {
                player.hp -= 15 * dmgMult;
                reduceShieldDurability(); // 盾の耐久減少
                reduceArmorDurability();
                spawnDamageText(player.px, player.py - 20, Math.round(15 * dmgMult));
                spawnParticles(player.px, player.py, '#e040fb');
                screenShake = 4;
                checkPlayerDeath();
            } else if (p.type === 'medusa_orb') {
                let dmg = Math.round(12 * dmgMult);
                player.hp -= dmg;
                reduceShieldDurability(); // 盾の耐久減少
                reduceArmorDurability();
                spawnDamageText(player.px, player.py - 20, dmg);
                spawnParticles(player.px, player.py, '#2ecc71');
                screenShake = 4;

                let medusa = bosses.find(b => b.id === 'medusa' && b.active && !b.dead);
                if (medusa) {
                    if (medusa.ultReady) {
                        // 必殺技がたまっている状態で攻撃が当たるたび60%で発動
                        if (Math.random() < 0.6) {
                            medusa.ultReady = false;
                            medusa.hitCount = 0;
                            triggerMedusaUltimate(medusa);
                        }
                    } else {
                        medusa.hitCount = (medusa.hitCount || 0) + 1;
                        if (medusa.hitCount >= 5) {
                            medusa.ultReady = true;
                            spawnDamageText(medusa.x * TS + TS / 2, medusa.y * TS - 35, "必殺技チャージ!");
                            spawnParticles(medusa.x * TS + TS / 2, medusa.y * TS + TS / 2, '#a3e635');
                        }
                    }
                }
                checkPlayerDeath();
            }
            projectiles.splice(i, 1);
            continue;
        }
        
        if (p.life <= 0) {
            spawnParticles(p.x, p.y, p.type === 'mazerian_homing' ? '#9b59b6' : '#2ecc71');
            projectiles.splice(i, 1);
        }
    }
}

function updateBosses(dt) {
    bosses.forEach(b => {
        if(!b.active || b.dead) return;
        
        let bx = b.x * TS + TS/2;
        let by = b.y * TS + TS/2;
        let dist = Math.hypot(player.px - bx, player.py - by);
        
        let mapBx = Math.floor(b.x);
        let mapBy = Math.floor(b.y);
        let mapPx = Math.floor(player.px / TS);
        let mapPy = Math.floor(player.py / TS);
        // 壁越しにプレイヤーが見えるかチェック
        let canSee = hasLineOfSight(mapBx, mapBy, mapPx, mapPy);

        b.stateTimer -= dt;
        let angle = Math.atan2(player.py - by, player.px - bx);

        // --- メデューサ専用AI ---
        if (b.id === 'medusa') {
            if (b.state === 'idle') {
                if (b.stateTimer <= 0) {
                    if (dist > TS * 16) {
                        b.stateTimer = 400;
                        return;
                    }
                    b.facing = Math.cos(angle) > 0 ? 1 : -1;
                    let r = Math.random();
                    if (r < 0.35) {
                        // 移動（ゆっくり距離を調整）
                        b.state = 'walk';
                        b.stateTimer = 700;
                    } else if (r < 0.68) {
                        // 攻撃1：5個の弾を拡散発射する準備
                        b.state = 'spread_prepare';
                        b.stateTimer = 550;
                        b.shootAngle = angle;
                    } else {
                        // 攻撃2：5個の弾を直進で一方向に発射する準備
                        b.state = 'line_prepare';
                        b.stateTimer = 550;
                        b.shootAngle = angle;
                    }
                }
            } else if (b.state === 'walk') {
                b.facing = Math.cos(angle) > 0 ? 1 : -1;
                let moveDir = dist < TS * 4 ? -1 : 1; // 近すぎたら少し離れる
                let speed = 1.6;
                let nx = bx + Math.cos(angle) * speed * moveDir * (dt / 16);
                let ny = by + Math.sin(angle) * speed * moveDir * (dt / 16);
                if (!checkCollision(nx, ny, 28)) {
                    b.x = (nx - TS / 2) / TS;
                    b.y = (ny - TS / 2) / TS;
                }
                if (b.stateTimer <= 0) {
                    b.state = 'idle';
                    b.stateTimer = 300;
                }
            } else if (b.state === 'spread_prepare') {
                b.facing = Math.cos(angle) > 0 ? 1 : -1;
                b.shootAngle = angle;
                if (b.stateTimer <= 0) {
                    // 5個の弾を扇状に拡散発射
                    let spreadOffsets = [-0.44, -0.22, 0, 0.22, 0.44];
                    let orbSpeed = 4.6;
                    spreadOffsets.forEach(offset => {
                        let a = b.shootAngle + offset;
                        projectiles.push({
                            x: bx,
                            y: by,
                            vx: Math.cos(a) * orbSpeed,
                            vy: Math.sin(a) * orbSpeed,
                            type: 'medusa_orb',
                            life: 3200
                        });
                    });
                    spawnParticles(bx, by, '#2ecc71');
                    b.state = 'idle';
                    b.stateTimer = 1400;
                }
            } else if (b.state === 'line_prepare') {
                b.facing = Math.cos(angle) > 0 ? 1 : -1;
                b.shootAngle = angle;
                if (b.stateTimer <= 0) {
                    // 5個の弾を直進で一方向に連射開始
                    b.state = 'line_shoot';
                    b.burstRemaining = 5;
                    b.burstTimer = 0;
                }
            } else if (b.state === 'line_shoot') {
                b.burstTimer -= dt;
                if (b.burstTimer <= 0 && b.burstRemaining > 0) {
                    let orbSpeed = 5.4;
                    projectiles.push({
                        x: bx,
                        y: by,
                        vx: Math.cos(b.shootAngle) * orbSpeed,
                        vy: Math.sin(b.shootAngle) * orbSpeed,
                        type: 'medusa_orb',
                        life: 3200
                    });
                    b.burstRemaining--;
                    b.burstTimer = 110; // 0.11秒間隔で一直線に5発発射
                }
                if (b.burstRemaining <= 0) {
                    b.state = 'idle';
                    b.stateTimer = 1400;
                }
            }
            return;
        }
        
        // AI行動遷移（ギーラ）
        if (b.state === 'idle') {
            if (b.stateTimer <= 0) {
                b.facing = Math.cos(angle) > 0 ? 1 : -1;
                
                // プレイヤーが遠い、または壁に遮られている場合は待機を継続
                if (dist > TS * 12 || !canSee) {
                    b.stateTimer = 500;
                    return;
                }

                let r = Math.random();
                if (dist < TS * 3.5) {
                    // 近い場合：斧攻撃準備（攻撃間隔を早く）
                    b.state = 'axe_prepare';
                    b.stateTimer = 350; 
                } else if (r < 0.5 && dist < TS * 8) {
                    // 中距離：突撃準備（準備も早く、速度は遅く）
                    b.state = 'charge_prepare';
                    b.stateTimer = 500; 
                    b.vx = Math.cos(angle) * 9;
                    b.vy = Math.sin(angle) * 9;
                } else {
                    // 移動
                    b.state = 'walk';
                    b.stateTimer = 500 + Math.random() * 500;
                }
            }
        } else if (b.state === 'walk') {
            b.facing = Math.cos(angle) > 0 ? 1 : -1;
            let speed = 2;
            b.x += (Math.cos(angle) * speed * (dt/16)) / TS;
            b.y += (Math.sin(angle) * speed * (dt/16)) / TS;
            if (b.stateTimer <= 0) b.state = 'idle';
        } else if (b.state === 'axe_prepare') {
            if (b.stateTimer <= 0) {
                b.state = 'axe_swing';
                b.stateTimer = 200;
                
                let currentDist = Math.hypot(player.px - bx, player.py - by);
                // 向きと角度から厳密な前方当たり判定を計算
                let angleToPlayer = Math.atan2(player.py - by, player.px - bx);
                let bossAngle = b.facing > 0 ? 0 : Math.PI;
                let angleDiff = Math.abs(angleToPlayer - bossAngle);
                if (angleDiff > Math.PI) angleDiff = 2 * Math.PI - angleDiff;
                
                // 斧はボスの前方90度以内、かつ壁越しでない場合に当たる
                if (currentDist < TS * 3.5 && angleDiff < Math.PI / 2 && canSee) { 
                    let dmgMult = getShieldDamageMultiplier() * getArmorDamageMultiplier();
                    player.hp -= 25 * dmgMult;
                    reduceShieldDurability(); // 盾の耐久減少
                    reduceArmorDurability();
                    checkMedusaPetrifyOnHit(b);
                    spawnDamageText(player.px, player.py - 20, Math.round(25 * dmgMult));
                    spawnParticles(player.px, player.py, '#e74c3c');
                    screenShake = 8;
                    checkPlayerDeath();
                }
            }
        } else if (b.state === 'axe_swing') {
            if (b.stateTimer <= 0) {
                b.state = 'idle';
                b.stateTimer = 300;
            }
        } else if (b.state === 'charge_prepare') {
            if (b.stateTimer <= 0) {
                b.state = 'charge';
                b.stateTimer = 800; 
            }
        } else if (b.state === 'charge') {
            // 突進処理
            let nx = b.x * TS + TS/2 + b.vx * (dt/16);
            let ny = b.y * TS + TS/2 + b.vy * (dt/16);
            
            if (checkCollision(nx, ny, 30)) {
                // 壁に激突したらスタン（気絶）
                b.state = 'stun';
                b.stateTimer = 1500;
                screenShake = 12;
            } else {
                b.x = (nx - TS/2) / TS;
                b.y = (ny - TS/2) / TS;
                
                let currentDist = Math.hypot(player.px - (b.x * TS + TS/2), player.py - (b.y * TS + TS/2));
                if (currentDist < TS * 2.0) { // 当たり判定を調整 (ケンタウロスのサイズアップに対応)
                    let dmgMult = getShieldDamageMultiplier() * getArmorDamageMultiplier();
                    player.hp -= 40 * dmgMult; 
                    reduceShieldDurability(); // 盾の耐久減少
                    reduceArmorDurability();
                    checkMedusaPetrifyOnHit(b);
                    spawnDamageText(player.px, player.py - 20, Math.round(40 * dmgMult));
                    spawnParticles(player.px, player.py, '#e74c3c');
                    screenShake = 15;
                    b.state = 'stun'; // プレイヤーに当たっても止まる
                    b.stateTimer = 1000;
                    
                    // ノックバックの方向を修正
                    let kbAngle = Math.atan2(player.py - (b.y * TS + TS/2), player.px - (b.x * TS + TS/2));
                    player.px += Math.cos(kbAngle) * 20; 
                    player.py += Math.sin(kbAngle) * 20;
                    
                    checkPlayerDeath();
                }
            }
            if (b.stateTimer <= 0 && b.state === 'charge') {
                b.state = 'idle';
                b.stateTimer = 300;
            }
        } else if (b.state === 'stun') {
            if (b.stateTimer <= 0) b.state = 'idle';
        }
    });
}

// --- アクション処理 ---
let lastActionTime = 0;
let currentInteractTarget = null;

window.addEventListener('keydown', (e) => {
    if(e.code === 'Space') {
        e.preventDefault();
        let now = Date.now();
        let currentItem = player.hotbar[player.selectedSlot];
        let cooldown = (currentItem && currentItem.startsWith('pickaxe_')) || currentItem === 'maze_pickaxe' ? 200 : 400; 
        if(now - lastActionTime > cooldown && !isPaused) { 
            lastActionTime = now;
            performAction();
        }
    }
    
    // 管理者メニュー起動 (4番スロット選択時、PとL同時押し)
    if(keys['KeyP'] && keys['KeyL'] && player.selectedSlot === 3) {
        openAdminMenu();
    }

    if(e.code === 'Digit1') selectSlot(0);
    if(e.code === 'Digit2') selectSlot(1);
    if(e.code === 'Digit3') selectSlot(2);
    if(e.code === 'Digit4') selectSlot(3);
    if(e.code === 'Digit5') selectSlot(4);
    if(e.code === 'KeyI') openInventory();
    if(e.code === 'KeyE') doInteract();
    if(e.code === 'KeyM') toggleMinimapZoom();
    if(e.code === 'Escape' && minimapZoomLevel === 3) {
        closeFullMap();
    }
});

window.addEventListener('mousedown', (e) => {
    // UI以外のキャンバスクリックでアクション
    if(e.target === canvas && !isPaused) {
        let now = Date.now();
        let currentItem = player.hotbar[player.selectedSlot];
        let cooldown = (currentItem && currentItem.startsWith('pickaxe_')) || currentItem === 'maze_pickaxe' ? 200 : 400;
        if(now - lastActionTime > cooldown) { 
            lastActionTime = now;
            performAction();
        }
    }
});

function checkInteractTarget() {
    let tPx = player.px;
    let tPy = player.py;
    let reach = TS;
    
    if(player.dir === 'up') tPy -= reach;
    else if(player.dir === 'down') tPy += reach;
    else if(player.dir === 'left') tPx -= reach;
    else if(player.dir === 'right') tPx += reach;

    currentInteractTarget = null;
    let promptEl = document.getElementById('interactPrompt');

    // 1. 石像とのインタラクト判定
    for(let s of statues) {
        if(map[s.y][s.x] !== TILE.FLOOR && map[s.y][s.x] !== TILE.HAY && map[s.y][s.x] !== TILE.ASH_FLOOR) continue; // 埋まっている時は調べられない
        // 石像が3x3になったので判定範囲を広げる
        if(Math.hypot(s.x*TS + TS/2 - tPx, s.y*TS + TS/2 - tPy) < TS*2.5) {
            currentInteractTarget = { type: 'statue', target: s };
            promptEl.innerText = "[E] 石像を調べる";
            promptEl.classList.remove('hidden');
            return;
        }
    }

    // 2. NPC(ヘイザ)とのインタラクト判定
    for(let n of npcs) {
        if(Math.hypot(n.x - tPx, n.y - tPy) < TS) {
            currentInteractTarget = { type: 'npc', target: n };
            if (n.type === 'heiza') {
                promptEl.innerText = "[E] 話しかける (ヘイザ)";
            } else if (n.type === 'girieve') {
                promptEl.innerText = "[E] 話しかける (ギリーヴ)";
            }
            promptEl.classList.remove('hidden');
            return;
        }
    }
    
    // 3. 作業台とのインタラクト判定
    let tx = Math.floor(tPx / TS);
    let ty = Math.floor(tPy / TS);
    if(tx >= 0 && tx < MAP_W && ty >= 0 && ty < MAP_H) {
        let t = map[ty][tx];
        if(t === TILE.WORKBENCH) {
            currentInteractTarget = { type: 'workbench', workbenchType: 'wood' };
            promptEl.innerText = "[E] 木の作業台を使う";
            promptEl.classList.remove('hidden');
            return;
        } else if(t === TILE.WORKBENCH_ROCK) {
            currentInteractTarget = { type: 'workbench', workbenchType: 'rock' };
            promptEl.innerText = "[E] 石の作業台を使う";
            promptEl.classList.remove('hidden');
            return;
        } else if(t === TILE.WORKBENCH_SWAMP) {
            currentInteractTarget = { type: 'workbench', workbenchType: 'swamp' };
            promptEl.innerText = "[E] 沼地の作業台を使う";
            promptEl.classList.remove('hidden');
            return;
        } else if(t === TILE.WORKBENCH_WITHERED) {
            currentInteractTarget = { type: 'workbench', workbenchType: 'withered' };
            promptEl.innerText = "[E] 廃離の作業台を使う";
            promptEl.classList.remove('hidden');
            return;
        } else if(t === TILE.ANVIL) {
            currentInteractTarget = { type: 'workbench', workbenchType: 'anvil' };
            promptEl.innerText = "[E] 金床を使う";
            promptEl.classList.remove('hidden');
            return;
        } else if(t >= TILE.DIRT_CHEST && t <= TILE.MEDUSA_CHEST) {
            currentInteractTarget = { type: 'chest', x: tx, y: ty, tile: t };
            promptEl.innerText = "[E] チェストを開く";
            promptEl.classList.remove('hidden');
            return;
        }
    }
    
    promptEl.classList.add('hidden');
}

function doInteract() {
    if(!currentInteractTarget || isPaused || player.petrifyTimer > 0 || player.stunTimer > 0) return;
    
    if(currentInteractTarget.type === 'statue') {
        handleStatueInteract(currentInteractTarget.target);
    } else if(currentInteractTarget.type === 'npc') {
        let n = currentInteractTarget.target;
        if (n.type === 'heiza') {
            isPaused = true;
            document.getElementById('indexOverlay').classList.remove('hidden');
        } else if (n.type === 'girieve') {
            openGirieveMenu();
        }
    } else if(currentInteractTarget.type === 'workbench') {
        openGenericCraftMenu(currentInteractTarget.workbenchType);
    } else if (currentInteractTarget.type === 'chest') {
        openChest(currentInteractTarget.x, currentInteractTarget.y);
    }
}

function performAction() {
    if (player.petrifyTimer > 0 || player.stunTimer > 0) return;
    // プレイヤーの目の前の座標を計算
    let tPx = player.px;
    let tPy = player.py;
    let reach = TS;
    
    if(player.dir === 'up') tPy -= reach;
    else if(player.dir === 'down') tPy += reach;
    else if(player.dir === 'left') tPx -= reach;
    else if(player.dir === 'right') tPx += reach;

    let tx = Math.floor(tPx / TS);
    let ty = Math.floor(tPy / TS);
    
    let currentItemId = player.hotbar[player.selectedSlot];

    // ボスへの攻撃判定
    let bossHit = false;
    let checkAttackBoss = (b) => {
        if(b.active && !b.dead && Math.hypot(b.x*TS + TS/2 - tPx, b.y*TS + TS/2 - tPy) < TS*1.5) {
            bossHit = true;
        if(currentItemId && (currentItemId.startsWith('sword_') || currentItemId === 'geera_axe')) {
            let atk = (ITEM_DATA[currentItemId] && ITEM_DATA[currentItemId].type === 'weapon') ? ITEM_DATA[currentItemId].atk : player.attackPower;
            
            // ギーラアックスの火力計算
            if (currentItemId === 'geera_axe') {
                let geeraBonus = Math.min(45, Math.floor(player.continuousMoveTime / 500) * 5); // 0.5秒ごとに+5、最大+45
                atk = 30 + geeraBonus;
            }

            b.hp -= atk;
            if (ITEM_DATA[currentItemId] && ITEM_DATA[currentItemId].petrify) {
                b.petrifyTimer = Math.max(b.petrifyTimer || 0, 500);
                spawnParticles(b.x*TS + TS/2, b.y*TS + TS/2, '#ffffff');
            }
            reduceDurability(currentItemId); // 武器の耐久減少
            spawnDamageText(b.x*TS + TS/2, b.y*TS, atk);
            spawnParticles(tPx, tPy, b.color); 
            screenShake = 3;
                
                if(b.hp <= 0) {
                    b.dead = true;
                    if(b === finalBoss) {
                        isPaused = true;
                        document.getElementById('clearOverlay').classList.remove('hidden');
                    } else if (b.id === 'north_boss') {
                        placeBossChest(b.x, b.y, 'geera_chest', TILE.GEERA_CHEST);
                    } else if (b.id === 'medusa') {
                        placeBossChest(b.x, b.y, 'medusa_chest', TILE.MEDUSA_CHEST);
                    } else {
                        player.bossFragments += b.drop;
                        updateHUD();
                    }
                }
            } else {
                spawnDamageText(b.x*TS + TS/2, b.y*TS, 0); 
                showDialog("ヒント", "敵にダメージを与えるには[剣]を装備しろ！");
            }
        }
    };

    checkAttackBoss(finalBoss);
    if(!bossHit) bosses.forEach(b => checkAttackBoss(b));
    
    // 山羊への攻撃判定
    let goatHit = false;
    let checkAttackGoat = (g) => {
        if(Math.hypot(g.x*TS + TS/2 - tPx, g.y*TS + TS/2 - tPy) < TS*1.5) {
            goatHit = true;
        if(currentItemId && (currentItemId.startsWith('sword_') || currentItemId === 'geera_axe')) {
            let atk = (ITEM_DATA[currentItemId] && ITEM_DATA[currentItemId].type === 'weapon') ? ITEM_DATA[currentItemId].atk : player.attackPower;
            if (currentItemId === 'geera_axe') {
                let geeraBonus = Math.min(45, Math.floor(player.continuousMoveTime / 500) * 5);
                atk = 30 + geeraBonus;
            }
            g.hp -= atk;
            if (ITEM_DATA[currentItemId] && ITEM_DATA[currentItemId].petrify) {
                g.petrifyTimer = Math.max(g.petrifyTimer || 0, 500);
                spawnParticles(g.x*TS + TS/2, g.y*TS + TS/2, '#ffffff');
            }
            reduceDurability(currentItemId); // 武器の耐久減少
            g.hostile = true; // 攻撃されると敵対する
            spawnDamageText(g.x*TS + TS/2, g.y*TS, atk);
                spawnParticles(tPx, tPy, '#ffffff'); // 毛が散るエフェクト
                screenShake = 2;
            } else {
                spawnDamageText(g.x*TS + TS/2, g.y*TS, 0); 
            }
        }
    };
    if (!bossHit) goats.forEach(g => checkAttackGoat(g));
    
    // ブル（牛）への攻撃判定
    let bullHit = false;
    let checkAttackBull = (b) => {
        if (Math.hypot(b.x * TS + TS / 2 - tPx, b.y * TS + TS / 2 - tPy) < TS * 1.5) {
            bullHit = true;
            if (currentItemId && (currentItemId.startsWith('sword_') || currentItemId === 'geera_axe')) {
                let atk = (ITEM_DATA[currentItemId] && ITEM_DATA[currentItemId].type === 'weapon') ? ITEM_DATA[currentItemId].atk : player.attackPower;
                if (currentItemId === 'geera_axe') {
                    let geeraBonus = Math.min(45, Math.floor(player.continuousMoveTime / 500) * 5);
                    atk = 30 + geeraBonus;
                }
                b.hp -= atk;
                if (ITEM_DATA[currentItemId] && ITEM_DATA[currentItemId].petrify) {
                    b.petrifyTimer = Math.max(b.petrifyTimer || 0, 500);
                    spawnParticles(b.x * TS + TS / 2, b.y * TS + TS / 2, '#ffffff');
                }
                reduceDurability(currentItemId);
                spawnDamageText(b.x * TS + TS / 2, b.y * TS, atk);
                spawnParticles(tPx, tPy, '#8b5a2b');
                screenShake = 2;
            } else {
                spawnDamageText(b.x * TS + TS / 2, b.y * TS, 0);
            }
        }
    };
    if (!bossHit && !goatHit) bulls.forEach(b => checkAttackBull(b));

    // カエルへの攻撃判定
    let frogHit = false;
    let checkAttackFrog = (f) => {
        if(Math.hypot(f.x*TS + TS/2 - tPx, f.y*TS + TS/2 - tPy) < TS*1.5) {
            frogHit = true;
        if(currentItemId && (currentItemId.startsWith('sword_') || currentItemId === 'geera_axe')) {
            let atk = (ITEM_DATA[currentItemId] && ITEM_DATA[currentItemId].type === 'weapon') ? ITEM_DATA[currentItemId].atk : player.attackPower;
            if (currentItemId === 'geera_axe') {
                let geeraBonus = Math.min(45, Math.floor(player.continuousMoveTime / 500) * 5);
                atk = 30 + geeraBonus;
            }
            f.hp -= atk;
            if (ITEM_DATA[currentItemId] && ITEM_DATA[currentItemId].petrify) {
                f.petrifyTimer = Math.max(f.petrifyTimer || 0, 500);
                spawnParticles(f.x*TS + TS/2, f.y*TS + TS/2, '#ffffff');
            }
            reduceDurability(currentItemId); // 武器の耐久減少
            f.hostile = true; // 攻撃されると敵対する
            spawnDamageText(f.x*TS + TS/2, f.y*TS, atk);
                spawnParticles(tPx, tPy, '#2ecc71'); // 緑色の血エフェクト
                screenShake = 2;
                
                // カエル討伐時のギリーヴ出現ロジック
                if (f.hp <= 0 && !f.dead) {
                    f.dead = true; // 重複処理防止
                    frogKillCount++;
                    if (frogKillCount >= 3) {
                        frogKillCount = 0;
                        // 古いギリーヴを消去して新しいギリーヴを配置
                        npcs = npcs.filter(n => n.type !== 'girieve');
                        npcs.push({ x: f.x * TS + TS/2, y: f.y * TS + TS/2, type: 'girieve' });
                        spawnDamageText(f.x * TS + TS/2, f.y * TS - 30, "ギリーヴ出現!");
                    }
                }
            } else {
                spawnDamageText(f.x*TS + TS/2, f.y*TS, 0); 
            }
        }
    };
    if (!bossHit && !goatHit && !bullHit) frogs.forEach(f => checkAttackFrog(f));

    // メイズレアンへの攻撃判定
    let mazerianHit = false;
    let checkAttackMazerian = (m) => {
        if(Math.hypot(m.x*TS + TS/2 - tPx, m.y*TS + TS/2 - tPy) < TS*1.5) {
            mazerianHit = true;
            if(currentItemId && (currentItemId.startsWith('sword_') || currentItemId === 'geera_axe')) {
                // 必殺技：攻撃を3回以上行っており、ワープ残り回数がある場合はダメージを無効化してワープ回避
                if ((m.attackCount || 0) >= 3 && (m.warpUsesLeft || 0) > 0) {
                    m.attackCount = 0;
                    m.warpUsesLeft--;
                    
                    let oldMx = m.x * TS + TS/2;
                    let oldMy = m.y * TS + TS/2;
                    spawnParticles(oldMx, oldMy, '#e040fb');
                    spawnDamageText(oldMx, oldMy - 20, "ワープ回避!");
                    
                    // 近くの床タイルを探してワープ
                    let candidates = [];
                    let baseX = Math.floor(m.x);
                    let baseY = Math.floor(m.y);
                    for (let dy = -6; dy <= 6; dy++) {
                        for (let dx = -6; dx <= 6; dx++) {
                            let dist = Math.hypot(dx, dy);
                            if (dist >= 2 && dist <= 6) {
                                let nx = baseX + dx;
                                let ny = baseY + dy;
                                if (nx >= 0 && nx < MAP_W && ny >= 0 && ny < MAP_H && map[ny][nx] === TILE.FLOOR) {
                                    candidates.push({x: nx, y: ny});
                                }
                            }
                        }
                    }
                    if (candidates.length > 0) {
                        let dest = candidates[Math.floor(Math.random() * candidates.length)];
                        m.x = dest.x;
                        m.y = dest.y;
                        spawnParticles(m.x * TS + TS/2, m.y * TS + TS/2, '#9b59b6');
                    }
                    return;
                }

                let atk = (ITEM_DATA[currentItemId] && ITEM_DATA[currentItemId].type === 'weapon') ? ITEM_DATA[currentItemId].atk : player.attackPower;
                if (currentItemId === 'geera_axe') {
                    let geeraBonus = Math.min(45, Math.floor(player.continuousMoveTime / 500) * 5);
                    atk = 30 + geeraBonus;
                }
                m.hp -= atk;
                if (ITEM_DATA[currentItemId] && ITEM_DATA[currentItemId].petrify) {
                    m.petrifyTimer = Math.max(m.petrifyTimer || 0, 500);
                    spawnParticles(m.x*TS + TS/2, m.y*TS + TS/2, '#ffffff');
                }
                reduceDurability(currentItemId); // 武器の耐久減少
                spawnDamageText(m.x*TS + TS/2, m.y*TS, atk);
                spawnParticles(tPx, tPy, '#9b59b6');
                screenShake = 2;
                if (m.hp <= 0) {
                    addItemOrDrop('mazerian', 1, m.x * TS + TS / 2, m.y * TS + TS / 2);
                    updateHUD();
                }
            } else {
                spawnDamageText(m.x*TS + TS/2, m.y*TS, 0);
            }
        }
    };
    if (!bossHit && !goatHit && !bullHit && !frogHit) mazerians.forEach(m => checkAttackMazerian(m));

    // 忘却の木への攻撃判定
    let forgottenTreeHit = false;
    let checkAttackForgottenTree = (ft) => {
        if (Math.hypot(ft.x * TS + TS / 2 - tPx, ft.y * TS + TS / 2 - tPy) < TS * 1.5) {
            forgottenTreeHit = true;
        if (currentItemId && (currentItemId.startsWith('sword_') || currentItemId === 'geera_axe')) {
            let atk = (ITEM_DATA[currentItemId] && ITEM_DATA[currentItemId].type === 'weapon') ? ITEM_DATA[currentItemId].atk : player.attackPower;
            if (currentItemId === 'geera_axe') {
                let geeraBonus = Math.min(45, Math.floor(player.continuousMoveTime / 500) * 5);
                atk = 30 + geeraBonus;
            }
            ft.hp -= atk;
            if (ITEM_DATA[currentItemId] && ITEM_DATA[currentItemId].petrify) {
                ft.petrifyTimer = Math.max(ft.petrifyTimer || 0, 500);
                spawnParticles(ft.x*TS + TS/2, ft.y*TS + TS/2, '#ffffff');
            }
            reduceDurability(currentItemId); // 武器の耐久減少
            spawnDamageText(ft.x * TS + TS / 2, ft.y * TS, atk);
            spawnParticles(tPx, tPy, '#cbd5e1');
                screenShake = 2;
                if (ft.hp <= 0) {
                    dropForgottenTreeReward(ft.x * TS + TS / 2, ft.y * TS + TS / 2);
                    updateHUD();
                }
            } else {
                spawnDamageText(ft.x * TS + TS / 2, ft.y * TS, 0);
            }
        }
    };
    if (!bossHit && !goatHit && !bullHit && !frogHit && !mazerianHit) forgottenTrees.forEach(ft => checkAttackForgottenTree(ft));
    
    if(bossHit || goatHit || bullHit || frogHit || mazerianHit || forgottenTreeHit) return;

    let itemData = currentItemId ? ITEM_DATA[currentItemId] : null;

    // 消耗品・アクティブアイテムの使用
    if (itemData && (itemData.type === 'consumable' || itemData.type === 'active_item')) {
        if (currentItemId === 'compass') {
            if (!player.hasCompass) {
                player.hasCompass = true;
                player.inventory.compass--;
                if (player.inventory.compass <= 0) player.hotbar[player.selectedSlot] = null;
                updateHUD();
                spawnParticles(player.px, player.py, '#a855f7');
                showDialog("コンパス起動", "マスターコアの居場所がミニマップに表示された！");
            } else {
                showDialog("ヒント", "すでにマスターコアの場所は判明している。");
            }
            return;
        } else if (currentItemId === 'heiza_compass') {
            if (!player.hasHeizaCompass) {
                player.hasHeizaCompass = true;
                player.inventory.heiza_compass--;
                if (player.inventory.heiza_compass <= 0) player.hotbar[player.selectedSlot] = null;
                updateHUD();
                spawnParticles(player.px, player.py, '#facc15');
                showDialog("ヘイザのコンパス起動", "ヘイザの居場所がミニマップに表示された！");
            } else {
                showDialog("ヒント", "すでにヘイザの場所は判明している。");
            }
            return;
        } else if (currentItemId === 'withered_compass') {
            if (!player.hasWitheredCompass) {
                player.hasWitheredCompass = true;
                player.inventory.withered_compass--;
                if (player.inventory.withered_compass <= 0) player.hotbar[player.selectedSlot] = null;
                updateHUD();
                spawnParticles(player.px, player.py, '#2ecc71');
                showDialog("廃離のコンパス起動", "メデューサの居場所がミニマップに表示された！");
            } else {
                showDialog("ヒント", "すでにメデューサの場所は判明している。");
            }
            return;
        } else if (currentItemId === 'maze_summon') {
            let geera = bosses.find(b => b.id === 'north_boss');
            if (geera) {
                if (geera.dead) {
                    player.inventory.maze_summon--;
                    if (player.inventory.maze_summon <= 0) player.hotbar[player.selectedSlot] = null;
                    
                    geera.dead = false;
                    geera.hp = geera.maxHp;
                    geera.active = true;
                    // プレイヤーの少し横に復活
                    geera.x = player.px / TS + 2; 
                    geera.y = player.py / TS;
                    geera.state = 'idle';
                    geera.stateTimer = 1000;
                    
                    updateHUD();
                    spawnParticles(geera.x * TS, geera.y * TS, geera.color);
                    showDialog("ギーラ復活", "メイズサモンの力により、ギーラが蘇った！");
                } else {
                    showDialog("ヒント", "ギーラはまだ生きている。");
                }
            }
            return;
        } else if (currentItemId === 'medusa_eye') {
            if (player.medusaEyeCooldown <= 0) {
                player.medusaEyeCooldown = 10000; // 10秒クールダウン
                spawnParticles(player.px, player.py, '#2ecc71');
                screenShake = 8;
                showDialog("メデューサアイ", "周囲の敵を石化させた！");
                
                let range = TS * 10; // 10マスの範囲
                let hitCount = 0;
                [bosses, goats, bulls, frogs, mazerians, forgottenTrees].forEach(group => {
                    group.forEach(enemy => {
                        let ex = (enemy.x || 0) * TS + TS/2;
                        let ey = (enemy.y || 0) * TS + TS/2;
                        if (enemy.hp > 0 && Math.hypot(ex - player.px, ey - player.py) < range) {
                            enemy.petrifyTimer = 3000;
                            spawnParticles(ex, ey, '#ffffff');
                            hitCount++;
                        }
                    });
                });
                if (hitCount === 0) spawnDamageText(player.px, player.py - 20, "不発...");
            } else {
                let cdSec = Math.ceil(player.medusaEyeCooldown / 1000);
                spawnDamageText(player.px, player.py - 20, `CD: ${cdSec}秒`);
            }
            return;
        }
    }

    // タイルへのアクション（設置・掘る・破壊する）
    if(tx >= 0 && tx < MAP_W && ty >= 0 && ty < MAP_H) {
        let t = map[ty][tx];

        // 【ブロックの設置】
        if(itemData && itemData.type === 'block' && (t === TILE.FLOOR || t === TILE.HAY || t === TILE.ASH_FLOOR)) {
            // 他のキャラクターと重なっていないか簡易チェック
            let canPlace = true;
            bosses.forEach(b => { if(!b.dead && Math.floor(b.x) === tx && Math.floor(b.y) === ty) canPlace = false; });
            if(Math.floor(player.px/TS) === tx && Math.floor(player.py/TS) === ty) canPlace = false; // 自分自身と重なる場所
            
            if(canPlace && player.inventory[currentItemId] > 0) {
                map[ty][tx] = itemData.tile;
                // 耐久度の初期設定
                if(itemData.tile === TILE.DIRT || (itemData.tile >= TILE.WORKBENCH && itemData.tile <= TILE.WOOD_CHEST) || itemData.tile === TILE.MUD_CHEST) blockHp[ty][tx] = 3;
                else if(itemData.tile === TILE.ASH || itemData.tile === TILE.ASH_CHEST) blockHp[ty][tx] = 1;
                else if(itemData.tile === TILE.ROCK || itemData.tile === TILE.ROCK_CHEST || itemData.tile === TILE.MAZE_CHEST) blockHp[ty][tx] = 5;
                else if(itemData.tile === TILE.IRON || itemData.tile === TILE.IRON_CHEST || itemData.tile === TILE.GEERA_CHEST || itemData.tile === TILE.MEDUSA_CHEST) blockHp[ty][tx] = 8;
                else if(itemData.tile === TILE.WITHERED_CHEST) blockHp[ty][tx] = 4;
                
                if (itemData.tile >= TILE.DIRT_CHEST && itemData.tile <= TILE.MEDUSA_CHEST) {
                    placedChests[`${tx},${ty}`] = {
                        type: currentItemId,
                        items: new Array(itemData.maxSlots).fill(null),
                        timer: 60000
                    };
                }

                player.inventory[currentItemId]--;
                if(player.inventory[currentItemId] === 0) {
                    player.hotbar[player.selectedSlot] = null; // 使い切ったら枠を空にする
                }
                updateHUD();
                spawnParticles(tPx, tPy, '#ffffff');
            } else if (!canPlace) {
                spawnDamageText(tPx, tPy, "置けない!");
            }
            return; // 設置した場合はここで終了
        } else if (itemData && itemData.type === 'fill_block' && (t === TILE.WATER || t === TILE.CHASM)) {
            // 埋め立て処理
            if(player.inventory[currentItemId] > 0) {
                map[ty][tx] = TILE.FLOOR; // 埋める
                blockHp[ty][tx] = 0;
                player.inventory[currentItemId]--;
                if(player.inventory[currentItemId] === 0) {
                    player.hotbar[player.selectedSlot] = null;
                }
                updateHUD();
                spawnParticles(tPx, tPy, '#795548');
            }
            return; // 埋めた場合はここで終了
        }

        // 【採掘・破壊】
　　if (t === TILE.ASH_FLOOR) return;
        if(t === TILE.DIRT || t === TILE.ASH || t === TILE.WITHERED_TREE || t === TILE.ROCK || t === TILE.IRON || t === TILE.BOMB || t === TILE.DEEP_DIRT || t === TILE.ROOT || (t >= TILE.WORKBENCH && t <= TILE.MEDUSA_CHEST && t !== TILE.WATER && t !== TILE.CHASM && t !== TILE.SWAMP_WATER && t !== TILE.MUD) || t === TILE.MAZE_BLOCK || t === TILE.MUD) {
            let pickPower = 0;
            if (currentItemId && ITEM_DATA[currentItemId] && ITEM_DATA[currentItemId].type === 'tool') pickPower = ITEM_DATA[currentItemId].power;
            else if (currentItemId === 'maze_pickaxe') pickPower = 10; // メイズアンチは採掘力10

            if(pickPower > 0) {
                // 深層の土は採掘力が3以上ないと掘れない
                if (t === TILE.DEEP_DIRT && pickPower < 3) {
                    spawnDamageText(tPx, tPy, "硬い!");
                    return;
                }

                // 灰のつるはしはすべてのブロックを1発破壊
                if (currentItemId === 'pickaxe_ash') {
                    blockHp[ty][tx] = 0;
                } else if (t === TILE.ASH) { // 灰ブロックもすべてのツールで1発破壊
                    blockHp[ty][tx] = 0;
                } else {
                    blockHp[ty][tx] -= pickPower;
                }
                
                reduceDurability(currentItemId); // ツールの耐久減少
                
                let color = t === TILE.ROCK ? '#95a5a6' : 
                            t === TILE.IRON ? '#7f8c8d' : 
                            t === TILE.DEEP_DIRT ? '#2d2d2d' : 
                            t === TILE.ROOT ? '#2ecc71' : 
                            (t >= TILE.WORKBENCH && t <= TILE.MEDUSA_CHEST && t !== TILE.WATER && t !== TILE.CHASM && t !== TILE.SWAMP_WATER && t !== TILE.MUD) ? '#8B4513' : 
                            t === TILE.MAZE_UNREAL ? '#3f51b5' : 
                            t === TILE.MAZE_BLOCK ? '#8e44ad' : 
                            t === TILE.MUD ? '#3e2723' : 
                            (t === TILE.ASH || t === TILE.WITHERED_TREE) ? '#bdc3c7' : '#8b4513';
                            
                spawnParticles(tPx, tPy, color);
                
                // 耐久度が0になったら破壊
                if(blockHp[ty][tx] <= 0) {
                    if (t >= TILE.DIRT_CHEST && t <= TILE.MEDUSA_CHEST) {
                        let chestKey = `${tx},${ty}`;
                        if (placedChests[chestKey]) {
                            placedChests[chestKey].items.forEach(item => {
                                if (item) spawnDroppedItem(tx*TS+TS/2, ty*TS+TS/2, item.id, item.count, item.durability);
                            });
                            delete placedChests[chestKey];
                        }

                        let typeId = Object.keys(ITEM_DATA).find(k => ITEM_DATA[k].tile === t);
                        if (typeId) addItemOrDrop(typeId, 1, tPx, tPy);
                        updateHUD();
                        map[ty][tx] = TILE.FLOOR;
                        spawnParticles(tPx, tPy, '#8b4513');
                    } else if (t === TILE.MUD && Math.random() < 0.333) {
                        // 泥のなだれ込み復活 (3回に1回)
                        blockHp[ty][tx] = 2; // 耐久度リセット
                        spawnDamageText(tPx, tPy, "泥なだれ!");
                        spawnParticles(tPx, tPy, '#3e2723');
                    } else if(t === TILE.BOMB) {
                        map[ty][tx] = TILE.BOMB_ACTIVE; // 当たり判定を残したまま起爆状態へ
                        activateBomb(tx, ty, 3000); 
                        spawnParticles(tPx, tPy, '#a0522d');
                    } else {
                        // アイテム取得（26種類目以降は地面にドロップ）
                        if(t === TILE.DIRT || t === TILE.DEEP_DIRT) addItemOrDrop('dirt', 1, tPx, tPy);
                        else if(t === TILE.MUD) addItemOrDrop('mud', 1, tPx, tPy);
                        else if(t === TILE.ASH) addItemOrDrop('ash', 1, tPx, tPy);
                        else if(t === TILE.WITHERED_TREE) addItemOrDrop('withered_wood', 1, tPx, tPy);
                        else if(t === TILE.ROCK) addItemOrDrop('rock', 1, tPx, tPy);
                        else if(t === TILE.IRON) addItemOrDrop('iron', 1, tPx, tPy);
                        else if(t === TILE.ROOT) addItemOrDrop('wood', 1, tPx, tPy);
                        else if(t === TILE.WORKBENCH) addItemOrDrop('workbench', 1, tPx, tPy);
                        else if(t === TILE.WORKBENCH_ROCK) addItemOrDrop('workbench_rock', 1, tPx, tPy);
                        else if(t === TILE.WORKBENCH_SWAMP) addItemOrDrop('workbench_swamp', 1, tPx, tPy);
                        else if(t === TILE.WORKBENCH_WITHERED) addItemOrDrop('workbench_withered', 1, tPx, tPy);
                        else if(t === TILE.ANVIL) addItemOrDrop('anvil', 1, tPx, tPy);
                        else if(t === TILE.MAZE_BLOCK) addItemOrDrop('mazerian', 1, tPx, tPy);
                        
                        updateHUD();

                        // 廃離の森のオブジェクトを壊した場合は灰色の地面を残す
                        map[ty][tx] = (t === TILE.ASH || t === TILE.WITHERED_TREE) ? TILE.ASH_FLOOR : TILE.FLOOR;
                        // 石像の中心を掘った場合、周囲3x3も一気に露出させる
                        statues.forEach(s => {
                            if(s.x === tx && s.y === ty) {
                                for(let oy=-1; oy<=1; oy++) {
                                    for(let ox=-1; ox<=1; ox++) {
                                        if (map[s.y+oy][s.x+ox] !== TILE.MAZE_BRICK && map[s.y+oy][s.x+ox] !== TILE.CHASM) {
                                            map[s.y+oy][s.x+ox] = TILE.FLOOR;
                                            blockHp[s.y+oy][s.x+ox] = 0;
                                        }
                                    }
                                }
                            }
                        });
                    }
                }
            } else {
                showDialog("ヒント", "ブロックを壊すには[つるはし]を装備しろ！カバン[I]からセットできる。");
            }
        } else if (t === TILE.VOID || t === TILE.MAZE_BRICK || t === TILE.MAZE_UNREAL || t === TILE.WATER || t === TILE.CHASM || t === TILE.SWAMP_WATER) {
            spawnDamageText(tPx, tPy, "破壊不能!");
        }
    }
}

// ボス討伐時のチェスト設置ヘルパー
function placeBossChest(bx, by, chestId, tileId) {
    let tx = Math.floor(bx);
    let ty = Math.floor(by);
    
    // チェストを置ける場所を探す（ボスと同じマスかその周囲）
    if (map[ty][tx] !== TILE.FLOOR && map[ty][tx] !== TILE.ASH_FLOOR) {
        const offsets = [[0,1],[1,0],[0,-1],[-1,0],[1,1],[-1,1],[1,-1],[-1,-1]];
        for(let [ox, oy] of offsets) {
            if (map[ty+oy][tx+ox] === TILE.FLOOR || map[ty+oy][tx+ox] === TILE.ASH_FLOOR) {
                tx += ox; ty += oy; break;
            }
        }
    }
    
    // それでもダメなら強制上書き
    map[ty][tx] = tileId;
    blockHp[ty][tx] = 8;
    
    let chestItems = new Array(5).fill(null);
    if (chestId === 'geera_chest') {
        chestItems[0] = { id: 'geera_plate', count: 1 };
        chestItems[1] = { id: 'geera_scale', count: 1 + Math.floor(Math.random()*3) };
        chestItems[2] = { id: 'iron', count: 5 + Math.floor(Math.random()*5) };
        if (Math.random() < 0.2) chestItems[3] = { id: 'geera_axe', count: 1, durability: ITEM_DATA['geera_axe'].maxDurability };
    } else if (chestId === 'medusa_chest') {
        chestItems[0] = { id: 'medusa_plate', count: 1 };
        chestItems[1] = { id: 'medusa_hair', count: 1 + Math.floor(Math.random()*3) };
        chestItems[2] = { id: 'withered_wood', count: 5 + Math.floor(Math.random()*5) };
        if (Math.random() < 0.2) chestItems[3] = { id: 'medusa_eye', count: 1 };
    }
    
    placedChests[`${tx},${ty}`] = { type: chestId, items: chestItems, timer: 0 };
    spawnParticles(tx*TS+TS/2, ty*TS+TS/2, '#f1c40f');
    spawnDamageText(tx*TS+TS/2, ty*TS-20, "チェスト出現!");
}

function checkCompass() {
    // 以前は自動取得だったが、ショップ購入に変更したため自動処理は削除
}

// --- 爆弾とエフェクトロジック ---
function activateBomb(x, y, delay) {
    // すでに点滅中リストになければ追加
    if(!activeBombs.some(b => b.x === x && b.y === y)) {
        activeBombs.push({ x: x, y: y, timer: delay, maxTimer: delay });
    }
}

function updateBombs(dt) {
    for(let i = activeBombs.length - 1; i >= 0; i--) {
        let b = activeBombs[i];
        b.timer -= dt;
        if(b.timer <= 0) {
            explode(b.x, b.y);
            activeBombs.splice(i, 1);
        }
    }
}

function explode(cx, cy) {
    let radius = 2; // 爆発半径 (5x5)
    screenShake = 12; // 画面揺れ
    
    // 爆発エフェクトパーティクル
    for(let i=0; i<40; i++) {
        particles.push({
            x: cx*TS + TS/2, y: cy*TS + TS/2,
            vx: (Math.random() - 0.5) * 15,
            vy: (Math.random() - 0.5) * 15,
            life: 1.0,
            decay: 0.02 + Math.random() * 0.02,
            color: Math.random() > 0.5 ? '#e74c3c' : '#f1c40f',
            size: 4 + Math.random() * 6
        });
    }

    for(let y = cy - radius; y <= cy + radius; y++) {
        for(let x = cx - radius; x <= cx + radius; x++) {
            if(x >= 0 && x < MAP_W && y >= 0 && y < MAP_H) {
                // チェビシェフ距離（正方形の範囲）
                if(Math.max(Math.abs(x-cx), Math.abs(y-cy)) <= radius) {
                    let t = map[y][x];
                    if(t === TILE.BOMB || t === TILE.BOMB_ACTIVE) {
                        map[y][x] = TILE.FLOOR;
                        blockHp[y][x] = 0;
                        // 連鎖誘爆は短いディレイで
                        activateBomb(x, y, 250); 
                    } else if(t !== TILE.VOID && t !== TILE.FLOOR && t !== TILE.HAY && t !== TILE.MAZE_BRICK) {
                        // 岩や土も破壊される (メイズアンレンガ・虚無は破壊不可)
                        map[y][x] = TILE.FLOOR;
                        blockHp[y][x] = 0;
                    }
                }
            }
        }
    }

    // プレイヤーへのダメージ判定
    let px = Math.floor(player.px / TS);
    let py = Math.floor(player.py / TS);
    if(Math.max(Math.abs(px-cx), Math.abs(py-cy)) <= radius) {
        let dmgMult = getShieldDamageMultiplier() * getArmorDamageMultiplier();
        player.hp -= 30 * dmgMult; // 爆発ダメージ
        reduceShieldDurability(); // 盾の耐久減少
        reduceArmorDurability();
        spawnDamageText(player.px, player.py - 20, Math.round(30 * dmgMult));
        updateHUD();
        if(player.hp <= 0) {
            isPaused = true;
            document.getElementById('gameoverOverlay').classList.remove('hidden');
        }
    }
}

function spawnParticles(x, y, color) {
    for(let i=0; i<15; i++) {
        particles.push({
            x: x, y: y,
            vx: (Math.random() - 0.5) * 6,
            vy: (Math.random() - 0.5) * 6,
            life: 1.0,
            decay: 0.03,
            color: color,
            size: 3 + Math.random() * 3
        });
    }
}

function spawnDamageText(x, y, amount) {
    damageTexts.push({ x: x, y: y, amount: amount, life: 1.0 });
}

function updateVisuals(dt) {
    // パーティクル更新
    for(let i = particles.length - 1; i >= 0; i--) {
        let p = particles[i];
        p.x += p.vx;
        p.y += p.vy;
        p.life -= p.decay * (dt/16);
        if(p.life <= 0) particles.splice(i, 1);
    }
    // ダメージテキスト更新
    for(let i = damageTexts.length - 1; i >= 0; i--) {
        let d = damageTexts[i];
        d.y -= 1 * (dt/16);
        d.life -= 0.02 * (dt/16);
        if(d.life <= 0) damageTexts.splice(i, 1);
    }
    // 画面揺れ減衰
    if(screenShake > 0) {
        screenShake *= 0.9;
        if(screenShake < 0.5) screenShake = 0;
    }
    if (whiteFlashAlpha > 0) {
        whiteFlashAlpha = Math.max(0, whiteFlashAlpha - 0.025 * (dt / 16));
    }
}

function updateMazeUnreals(dt) {
    mazeUnreals.forEach(u => {
        u.timer -= dt;
        if (u.timer <= 0) {
            u.timer = 300000;
            for (let dy = -2; dy <= 2; dy++) {
                for (let dx = -2; dx <= 2; dx++) {
                    if (dx === 0 && dy === 0) continue;
                    let nx = u.x + dx;
                    let ny = u.y + dy;
                    if (nx >= 0 && nx < MAP_W && ny >= 0 && ny < MAP_H && map[ny][nx] === TILE.FLOOR) {
                        if (Math.random() < 0.35) {
                            map[ny][nx] = TILE.MAZE_BLOCK;
                            blockHp[ny][nx] = 5;
                        }
                    }
                }
            }
        }
    });
}

function updateChests(dt) {
    for (let key in placedChests) {
        let chest = placedChests[key];
        if (chest.type === 'mud_chest' || chest.type === 'ash_chest') {
            chest.timer = (chest.timer || 60000) - dt;
            if (chest.timer <= 0) {
                let coords = key.split(',');
                let cx = parseInt(coords[0]);
                let cy = parseInt(coords[1]);
                let px = cx * TS + TS / 2;
                let py = cy * TS + TS / 2;
                
                if (chest.type === 'mud_chest') {
                    let item = chest.items[0];
                    if (item && item.id) {
                        if (Math.random() < 0.5) {
                            item.count *= 2;
                            spawnDamageText(px, py - 20, "泥チェスト増幅!");
                            spawnParticles(px, py, '#8d6e63');
                        } else {
                            item.count = Math.floor(item.count / 2);
                            spawnDamageText(px, py - 20, "泥チェスト減衰...");
                            spawnParticles(px, py, '#4e342e');
                            if (item.count <= 0) chest.items[0] = null;
                        }
                        // もし開いているチェストならUI更新
                        if (currentOpenedChest && currentOpenedChest.x === cx && currentOpenedChest.y === cy) {
                            renderMinecraftInventory();
                        }
                    }
                } else if (chest.type === 'ash_chest') {
                    let hasItems = false;
                    chest.items.forEach((item, idx) => {
                        if (item && item.id) {
                            spawnDroppedItem(px, py, item.id, item.count, item.durability);
                            chest.items[idx] = null;
                            hasItems = true;
                        }
                    });
                    spawnDamageText(px, py - 20, "灰チェスト暴発!");
                    spawnParticles(px, py, '#cbd5e1');
                    screenShake = 5;
                    
                    if (hasItems && currentOpenedChest && currentOpenedChest.x === cx && currentOpenedChest.y === cy) {
                        renderMinecraftInventory();
                    }
                }
                chest.timer = 60000; // 1分リセット
            }
        }
    }
}

function handleStatueInteract(s) {
    if (s.type === 'center') {
        openGenericCraftMenu('statue');
        return;
    }

    let guardian = bosses.find(b => b.targetStatue === s.type);
    let hasWestCore = (s.type === 'west' && (player.inventory.medusa_plate || 0) > 0);
    if (guardian && guardian.active && !guardian.dead && !hasWestCore) {
        if (s.type === 'west') {
            showDialog(s.name, `「守護者 ${guardian.name} を倒し、『メデューサの石板』を手に入れぬ限り、このコアは目覚めない…」`);
        } else if (s.type === 'north') {
            showDialog(s.name, `「守護者 ${guardian.name} を倒し、『ギーラの石板』を手に入れぬ限り、このコアは目覚めない…」`);
        } else {
            showDialog(s.name, `「守護者 ${guardian.name} を倒さぬ限り、このコアは目覚めない…」`);
        }
        return;
    }

    if (s.type === 'north') {
        if (player.activeStatues['north']) {
            openShop();
            return;
        }
        if (player.inventory.geera_plate > 0) {
            player.inventory.geera_plate--;
            player.activeStatues['north'] = true;
            player.cores++;
            spawnParticles(s.x*TS + TS/2, s.y*TS, s.color);
            updateHUD();
            showDialog(s.name + " 起動", "ギーラの石板をはめ込むと、コアが光り輝き始めた！<br>石像の力が解放され、取引が可能になった。");
            checkFinalBossUnlock();
        } else {
            showDialog(s.name, "「我を起動したくば、守護者を倒し『ギーラの石板』を捧げよ…」");
        }
    } else if (s.type === 'south' || s.type === 'east' || s.type === 'west') {
        if (player.activeStatues[s.type]) {
            showDialog(s.name, "「……」<br>すでにコアは起動している。");
            return;
        }

        // 西の石像の場合、専用アイテム「メデューサの石板」で起動
        if (s.type === 'west') {
            if ((player.inventory.medusa_plate || 0) > 0) {
                player.inventory.medusa_plate--;
                player.activeStatues['west'] = true;
                player.cores++;
                spawnParticles(s.x * TS + TS / 2, s.y * TS, s.color);
                updateHUD();
                showDialog(s.name + " 起動", "メデューサの石板をはめ込むと、石像が光り輝き始めた！");
                checkFinalBossUnlock();
            } else {
                showDialog(s.name, "「我を起動したくば、守護者を倒し『メデューサの石板』を捧げよ…」");
            }
            return;
        }

        if (player.bossFragments > 0) {
            player.bossFragments--;
            player.activeStatues[s.type] = true;
            player.cores++;
            spawnParticles(s.x*TS + TS/2, s.y*TS, s.color);
            updateHUD();
            showDialog(s.name + " 起動", "コアが光り輝き始めた！");
            checkFinalBossUnlock();
        } else {
            showDialog(s.name, "「我を起動したくば、守護者を倒し『ボスの欠片』を捧げよ…」");
        }
    }
}

function checkFinalBossUnlock() {
    if(player.cores >= 4 && !finalBoss.active) {
        finalBoss.active = true;
        setTimeout(() => {
            showDialog("マスターコア出現", "4つの石像が共鳴し、地下のどこかに『マスターコア』が姿を現した！<br>北の石像で『コンパス』を手に入れ、探し出して破壊しろ！");
        }, 1500);
    }
}

function openGenericCraftMenu(type) {
    isPaused = true;
    currentCraftType = type;
    
    let titleEl = document.getElementById('genericCraftTitle');
    let modalEl = document.getElementById('genericCraftModal');
    
    if (type === 'statue') {
        titleEl.innerText = '始まりの石像';
        titleEl.className = 'text-2xl sm:text-3xl font-bold mb-2 text-center text-orange-400';
        modalEl.style.borderColor = '#ea580c';
    } else if (type === 'wood') {
        titleEl.innerText = '木の作業台';
        titleEl.className = 'text-2xl sm:text-3xl font-bold mb-2 text-center text-yellow-500';
        modalEl.style.borderColor = '#ca8a04';
    } else if (type === 'rock') {
        titleEl.innerText = '石の作業台';
        titleEl.className = 'text-2xl sm:text-3xl font-bold mb-2 text-center text-gray-400';
        modalEl.style.borderColor = '#9ca3af';
    } else if (type === 'swamp') {
        titleEl.innerText = '沼地の作業台';
        titleEl.className = 'text-2xl sm:text-3xl font-bold mb-2 text-center text-emerald-500';
        modalEl.style.borderColor = '#10b981';
    } else if (type === 'withered') {
        titleEl.innerText = '廃離の作業台';
        titleEl.className = 'text-2xl sm:text-3xl font-bold mb-2 text-center text-slate-400';
        modalEl.style.borderColor = '#94a3b8';
    } else if (type === 'anvil') {
        titleEl.innerText = '金床';
        titleEl.className = 'text-2xl sm:text-3xl font-bold mb-2 text-center text-slate-300';
        modalEl.style.borderColor = '#475569';
    }
    
    renderCraftList();
    document.getElementById('genericCraftOverlay').classList.remove('hidden');
}

function renderCraftList() {
    let listEl = document.getElementById('genericCraftList');
    listEl.innerHTML = '';
    
    let recipes = CRAFT_RECIPES[currentCraftType] || [];
    
    recipes.forEach(recipe => {
        let canCraft = true;
        let costHtml = '';
        for (let mat in recipe.cost) {
            let req = recipe.cost[mat];
            let owned = player.inventory[mat] || 0;
            if (owned < req) canCraft = false;
            let matData = ITEM_DATA[mat];
            let color = owned >= req ? 'text-green-400' : 'text-red-400';
            costHtml += `<span class="bg-gray-800 px-2 py-0.5 rounded mr-1 whitespace-nowrap">${matData.icon} <span class="${color}">${owned}</span>/${req}</span>`;
        }
        
        if (recipe.unique && recipe.check()) {
            canCraft = false;
            costHtml = `<span class="text-yellow-500 text-sm font-bold">作成済み</span>`;
        }
        
        let itemData = ITEM_DATA[recipe.id];
        let iconHtml = itemData ? getItemSvg(recipe.id) : (recipe.icon || '❓');
        if (recipe.id === 'maze_pin') iconHtml = '<span class="text-3xl">📍</span>';

        let statsHtml = '';
        if (itemData && itemData.maxDurability) {
            let s = [];
            if (itemData.type === 'weapon') s.push(`<span class="text-red-300">⚔️${itemData.atk}</span>`);
            if (itemData.type === 'tool') s.push(`<span class="text-yellow-300">⛏️${itemData.power}</span>`);
            if (itemData.type === 'shield') s.push(`<span class="text-blue-300">🛡️${Math.floor(itemData.reduce * 100)}%</span>`);
            let durText = itemData.maxDurability === Infinity ? '∞' : itemData.maxDurability;
            s.push(`<span class="text-green-400">🔨${durText}</span>`);
            statsHtml = `<div class="text-xs mt-1 flex gap-2">${s.join('')}</div>`;
        }

        let btnClass = canCraft 
            ? "w-full bg-gray-800 hover:bg-gray-700 border border-gray-600 p-3 rounded-lg flex flex-col gap-2 transition cursor-pointer text-left shadow-md" 
            : "w-full bg-gray-800 border border-gray-700 opacity-60 p-3 rounded-lg flex flex-col gap-2 cursor-not-allowed text-left";
            
        let btn = document.createElement('button');
        btn.className = btnClass;
        btn.disabled = !canCraft;
        btn.innerHTML = `
            <div class="flex justify-between items-center w-full">
                <div class="flex items-center gap-3">
                    <div class="w-10 h-10 flex items-center justify-center bg-gray-900 rounded border border-gray-700">${iconHtml}</div>
                    <div class="flex flex-col">
                        <span class="font-bold text-gray-200 text-base">${recipe.name}</span>
                        ${statsHtml}
                    </div>
                </div>
                <div class="bg-cyan-900 text-cyan-300 text-xs px-3 py-1.5 rounded-full font-bold shadow-sm flex items-center gap-1 ${canCraft ? '' : 'hidden'}">作成 🔨</div>
            </div>
            <div class="flex flex-wrap gap-1 text-xs text-gray-400 items-center justify-end w-full border-t border-gray-700 pt-2 mt-1">
                ${costHtml}
            </div>
        `;
        
        btn.onclick = () => {
            if (!canCraft) return;
            // 素材消費
            for (let mat in recipe.cost) {
                player.inventory[mat] -= recipe.cost[mat];
            }
            
            if (recipe.onCraft) {
                recipe.onCraft();
            } else {
                addItemOrDrop(recipe.id, 1, player.px, player.py);
                spawnParticles(player.px, player.py, '#ffffff');
            }
            
            updateHUD();
            renderCraftList();
        };
        
        listEl.appendChild(btn);
    });
}

function closeGenericCraftMenu() {
    document.getElementById('genericCraftOverlay').classList.add('hidden');
    isPaused = false;
}

// --- ギリーヴ用メニュー ---
function openGirieveMenu() {
    isPaused = true;
    document.getElementById('girieveOverlay').classList.remove('hidden');
}

function closeGirieveMenu() {
    document.getElementById('girieveOverlay').classList.add('hidden');
    isPaused = false;
}

function openCharacterIndex() {
    document.getElementById('characterIndexOverlay').classList.remove('hidden');
}

function updateHUD() {
    document.getElementById('hpValueTxt').innerText = `${Math.max(0, player.hp)} / ${player.maxHp}`;
    document.getElementById('hpBarFill').style.width = `${(Math.max(0, player.hp) / player.maxHp) * 100}%`;
    document.getElementById('hpBarFill').className = player.hp < 30 ? 'bg-red-500 h-2.5 rounded-full' : (player.hp < 70 ? 'bg-yellow-500 h-2.5 rounded-full' : 'bg-teal-400 h-2.5 rounded-full');
    
    let curItem = player.hotbar[player.selectedSlot];
    let dispAtk = 0;
    let dispPick = 0;
    let dispShield = 0;
    
    // 基本ステータス適用
    player.attackPower = 1;
    player.pickaxePower = 1;

    if (curItem && ITEM_DATA[curItem]) {
        let d = ITEM_DATA[curItem];
        if (d.type === 'weapon') {
            dispAtk = d.atk;
            player.attackPower = d.atk;
            if (curItem === 'geera_axe') {
                let geeraBonus = Math.min(45, Math.floor((player.continuousMoveTime || 0) / 500) * 5);
                dispAtk = 30 + geeraBonus;
                player.attackPower = dispAtk;
            }
        }
        if (d.type === 'tool') {
            dispPick = d.power;
            player.pickaxePower = d.power;
        }
        if (d.type === 'shield') dispShield = Math.floor(d.reduce * 100);
    }
    
    // 防具での軽減率を合算して表示に反映 (盾の横に防具アイコンを追加するなど)
    let armorReduce = Math.floor((1 - getArmorDamageMultiplier()) * 100);

    // UI反映（未装備の時は0表示などにせず基礎値の1を表示するなら1）
    let eqTxt = document.getElementById('equipStatusText');
    if (!eqTxt) {
        // 左上UIを拡張して盾の軽減率を追加
        let parentNode = document.getElementById('atkValue').parentNode;
        parentNode.innerHTML = `剣ATK:<span id="atkValue">${dispAtk || player.attackPower}</span> / 採掘:<span id="pickValue">${dispPick || player.pickaxePower}</span> / 防御🛡️:<span id="shieldValue">${dispShield + armorReduce}</span>%`;
    } else {
        document.getElementById('atkValue').innerText = dispAtk || player.attackPower;
        document.getElementById('pickValue').innerText = dispPick || player.pickaxePower;
        document.getElementById('shieldValue').innerText = dispShield + armorReduce;
    }

    if(player.hasCompass) {
        document.getElementById('compassStatus').innerText = "入手済";
        document.getElementById('compassStatus').className = "text-cyan-400 font-bold";
    } else {
        document.getElementById('compassStatus').innerText = `未所持`;
        document.getElementById('compassStatus').className = "text-gray-500";
    }

    document.getElementById('fragmentCount').innerText = player.bossFragments;
    
    let shopRockCount = document.getElementById('shopRockCount');
    if (shopRockCount) shopRockCount.innerText = player.inventory.rock;
    let shopIronCount = document.getElementById('shopIronCount');
    if (shopIronCount) shopIronCount.innerText = player.inventory.iron;
    let shopWoodCount = document.getElementById('shopWoodCount');
    if (shopWoodCount) shopWoodCount.innerText = player.inventory.wood;
    let shopMazeCount2 = document.getElementById('shopMazeCount2');
    if (shopMazeCount2) shopMazeCount2.innerText = player.inventory.mazerian;

    // コア状況
    const cores = ['north', 'south', 'west', 'east'];
    cores.forEach(c => {
        let el = document.getElementById(`core-${c}`);
        if(player.activeStatues[c]) {
            el.className = "flex-1 py-1 text-center rounded border border-cyan-600 bg-cyan-900 text-cyan-400 text-sm font-bold shadow-[0_0_10px_rgba(34,211,238,0.5)] transition-all";
        } else {
            el.className = "flex-1 py-1 text-center rounded border border-gray-600 text-gray-500 text-sm transition-all";
        }
    });

    // 5スロットのホットバーUI構築
    let hotbarContainer = document.getElementById('hotbarContainer');
    hotbarContainer.innerHTML = '';
    for(let i=0; i<5; i++) {
        let itemId = player.hotbar[i];
        let data = itemId ? ITEM_DATA[itemId] : null;
        let isSelected = i === player.selectedSlot;
        
        let btn = document.createElement('button');
        btn.className = `w-14 h-14 rounded-lg border-2 ${isSelected ? 'border-cyan-400 bg-slate-700 scale-110 shadow-[0_0_10px_rgba(34,211,238,0.5)] z-10' : 'border-gray-700 bg-gray-800 hover:bg-gray-700'} flex flex-col items-center justify-center transition relative`;
        btn.onclick = () => selectSlot(i);
        
        let countHtml = '';
        let durabilityHtml = '';
        if (data) {
            let isEquip = itemId.startsWith('sword_') || itemId.startsWith('pickaxe_') || ['maze_pickaxe', 'maze_shield'].includes(itemId) || itemId.startsWith('shield_');
            if (isEquip && data.maxDurability) {
                let maxD = data.maxDurability;
                let curD = player.durability[itemId] !== undefined ? player.durability[itemId] : maxD;
                if (maxD === Infinity) {
                    durabilityHtml = `<div class="absolute bottom-1 left-1/2 transform -translate-x-1/2 w-10 h-1 bg-gray-900 rounded-full overflow-hidden"><div class="h-full bg-cyan-400" style="width: 100%"></div></div>`;
                } else {
                    let percent = (curD / maxD) * 100;
                    let color = percent > 50 ? 'bg-green-500' : (percent > 20 ? 'bg-yellow-500' : 'bg-red-500');
                    durabilityHtml = `<div class="absolute bottom-1 left-1/2 transform -translate-x-1/2 w-10 h-1 bg-gray-900 rounded-full overflow-hidden"><div class="h-full ${color}" style="width: ${percent}%"></div></div>`;
                }
                countHtml = `<span class="absolute top-0 right-1 text-[9px] text-gray-300 font-bold">x${player.inventory[itemId]}</span>`;
            } else {
                countHtml = `<span class="absolute bottom-0 right-1 text-[10px] text-yellow-400 font-bold">${player.inventory[itemId]}</span>`;
            }
            if (player.inventory[itemId] <= 0) btn.style.opacity = '0.4';
        }

        btn.innerHTML = `
            <span class="absolute top-0.5 left-1 text-[10px] ${isSelected ? 'text-cyan-300' : 'text-gray-500'} font-bold">${i+1}</span>
            <div class="flex items-center justify-center">${itemId ? getItemSvg(itemId) : ''}</div>
            ${durabilityHtml}
            ${countHtml}
        `;
        hotbarContainer.appendChild(btn);
    }
}

function showDialog(title, content) {
    isPaused = true; // ゲーム進行停止
    document.getElementById('dialogTitle').innerText = title;
    document.getElementById('dialogContent').innerHTML = content;
    document.getElementById('dialogOverlay').classList.remove('hidden');
}

document.getElementById('dialogClose').addEventListener('click', () => {
    document.getElementById('dialogOverlay').classList.add('hidden');
    isPaused = false;
});

function openShop() {
    isPaused = true;
    updateHUD();
    document.getElementById('shopMsg').innerText = "";
    document.getElementById('shopOverlay').classList.remove('hidden');
}

document.getElementById('shopClose').addEventListener('click', () => {
    document.getElementById('shopOverlay').classList.add('hidden');
    isPaused = false;
});

// ショップの購入処理
document.querySelectorAll('.shop-btn').forEach(btn => {
    btn.addEventListener('click', () => {
        let cost = parseInt(btn.dataset.cost);
        let item = btn.dataset.item;
        let currency = btn.dataset.currency;
        
        let hasEnough = false;
        if(currency === 'fragment' && player.bossFragments >= cost) hasEnough = true;
        else if(player.inventory[currency] >= cost) hasEnough = true;

        if(hasEnough) {
            if(currency === 'fragment') player.bossFragments -= cost;
            else player.inventory[currency] -= cost;

            let msg = "";
            if(item === 'maze_pickaxe') {
                let added = addItemOrDrop('maze_pickaxe', 1, player.px, player.py);
                msg = added ? "メイズアンチを獲得！圧倒的な採掘力！" : "カバンが満杯のため足元にドロップした！";
            } else if(item === 'maze_summon') {
                let added = addItemOrDrop('maze_summon', 1, player.px, player.py);
                msg = added ? "メイズサモンを獲得！使うとギーラを復活させられる！" : "カバンが満杯のため足元にドロップした！";
            } else if(item === 'withered_compass') {
                let added = addItemOrDrop('withered_compass', 1, player.px, player.py);
                msg = added ? "廃離のコンパスを獲得！カバンから装備して使え！" : "カバンが満杯のため足元にドロップした！";
            }
            updateHUD();
            let msgEl = document.getElementById('shopMsg');
            msgEl.innerText = msg;
            msgEl.className = "text-green-400 text-sm h-6 font-bold";
            setTimeout(() => { msgEl.innerText = ""; }, 2000);
        } else {
            let msgEl = document.getElementById('shopMsg');
        let currencyName = currency === 'fragment' ? "ボスの欠片" : ITEM_DATA[currency].name;
        msgEl.innerText = `${currencyName}が足りない！`;
        msgEl.className = "text-red-400 text-sm h-6 font-bold";
    }
    });
});

// --- 管理者メニュー処理 ---
let statusTapCount = 0;
let lastStatusTapTime = 0;

function handleStatusTap() {
    // 4番スロット（インデックス3）を選択中のみ有効
    if (player.selectedSlot !== 3) {
        statusTapCount = 0;
        return;
    }
    let now = Date.now();
    if (now - lastStatusTapTime < 800) {
        statusTapCount++;
    } else {
        statusTapCount = 1;
    }
    lastStatusTapTime = now;

    if (statusTapCount >= 5) {
        statusTapCount = 0;
        openAdminMenu();
    }
}

function openAdminMenu() {
    isPaused = true;
    document.getElementById('adminOverlay').classList.remove('hidden');
}

function closeAdminMenu() {
    document.getElementById('adminOverlay').classList.add('hidden');
    isPaused = false;
}

function adminRevealMap() {
    for(let y = 0; y < MAP_H; y++) {
        for(let x = 0; x < MAP_W; x++) {
            explored[y][x] = true;
        }
    }
    closeAdminMenu();
    showDialog("ADMIN", "マップの全容を明らかにしました。");
}

function adminGiveItems() {
    for (let key in player.inventory) {
        if (key.startsWith('sword_') || key.startsWith('pickaxe_') || ['maze_pickaxe', 'maze_shield', 'compass', 'heiza_compass', 'withered_compass', 'maze_summon'].includes(key)) {
            player.inventory[key] = 1;
        } else {
            player.inventory[key] = 99;
        }
    }
    player.bossFragments += 99;
    updateHUD();
    closeAdminMenu();
    showDialog("ADMIN", "全アイテムとボスの欠片をMAXまで取得しました。");
}

function adminHeal() {
    player.maxHp = 9999;
    player.hp = player.maxHp;
    player.attackPower = 9999;
    player.pickaxePower = 10;
    updateHUD();
    closeAdminMenu();
    showDialog("ADMIN", "HP、攻撃力、採掘力を最大化しました。");
}

function adminToggleNoclip() {
    isNoclip = !isNoclip;
    let btn = document.getElementById('adminNoclipBtn');
    if (isNoclip) {
        btn.innerText = "壁貫通: ON";
        btn.className = "w-full bg-green-600 hover:bg-green-500 text-white font-bold py-3 px-4 rounded-xl transition";
    } else {
        btn.innerText = "壁貫通: OFF";
        btn.className = "w-full bg-gray-700 hover:bg-gray-600 text-white font-bold py-3 px-4 rounded-xl transition";
    }
}

function clampFullMapCenter() {
    let viewTiles = MAP_W / fullMapZoom;
    if (viewTiles >= MAP_W) {
        fullMapCenterX = MAP_W / 2;
        fullMapCenterY = MAP_H / 2;
    } else {
        let half = viewTiles / 2;
        fullMapCenterX = Math.max(half, Math.min(MAP_W - half, fullMapCenterX));
        fullMapCenterY = Math.max(half, Math.min(MAP_H - half, fullMapCenterY));
    }
}

function updateFullMapZoomLabel() {
    let label = document.getElementById('fullMapZoomLabel');
    if (label) {
        label.innerText = `${fullMapZoom.toFixed(1)}x`;
    }
}

function zoomFullMap(factor, e) {
    if (e) e.stopPropagation();
    if (minimapZoomLevel !== 3) return;
    fullMapZoom = Math.max(1.0, Math.min(12.0, fullMapZoom * factor));
    clampFullMapCenter();
    updateFullMapZoomLabel();
}

function resetFullMapView(e) {
    if (e) e.stopPropagation();
    fullMapZoom = 1.0;
    fullMapCenterX = MAP_W / 2;
    fullMapCenterY = MAP_H / 2;
    updateFullMapZoomLabel();
}

function centerFullMapOnPlayer(e) {
    if (e) e.stopPropagation();
    if (fullMapZoom < 2.5) {
        fullMapZoom = 3.5;
    }
    fullMapCenterX = player.px / TS;
    fullMapCenterY = player.py / TS;
    clampFullMapCenter();
    updateFullMapZoomLabel();
}

function closeFullMap(e) {
    if (e) e.stopPropagation();
    if (minimapZoomLevel === 3) {
        toggleMinimapZoom();
    }
}

function handleMinimapContainerClick(e) {
    // 全体マップ表示中はドラッグ操作を優先し、クリックで勝手に閉じないようにする
    if (minimapZoomLevel === 3) return;
    toggleMinimapZoom();
}

function initFullMapInteractions() {
    const container = document.getElementById('minimapContainer');
    if (!container) return;

    let isDragging = false;
    let lastClientX = 0;
    let lastClientY = 0;
    let lastPinchDist = 0;

    // マウスホイールで拡大・縮小
    container.addEventListener('wheel', (e) => {
        if (minimapZoomLevel !== 3) return;
        e.preventDefault();
        e.stopPropagation();
        let factor = e.deltaY < 0 ? 1.2 : (1 / 1.2);
        zoomFullMap(factor);
    }, { passive: false });

    // マウスドラッグで移動
    container.addEventListener('mousedown', (e) => {
        if (minimapZoomLevel !== 3 || e.button !== 0) return;
        if (e.target.closest('button')) return;
        isDragging = true;
        lastClientX = e.clientX;
        lastClientY = e.clientY;
        container.style.cursor = 'grabbing';
    });

    window.addEventListener('mousemove', (e) => {
        if (!isDragging || minimapZoomLevel !== 3) return;
        let dx = e.clientX - lastClientX;
        let dy = e.clientY - lastClientY;
        lastClientX = e.clientX;
        lastClientY = e.clientY;

        let baseScale = Math.min(mCanvas.width, mCanvas.height) / MAP_W;
        let scale = baseScale * fullMapZoom;
        if (scale > 0) {
            fullMapCenterX -= dx / scale;
            fullMapCenterY -= dy / scale;
            clampFullMapCenter();
        }
    });

    window.addEventListener('mouseup', () => {
        if (isDragging) {
            isDragging = false;
            if (minimapZoomLevel === 3) {
                container.style.cursor = 'grab';
            }
        }
    });

    // タッチ操作（1本指ドラッグ移動・2本指ピンチズーム）
    container.addEventListener('touchstart', (e) => {
        if (minimapZoomLevel !== 3) return;
        if (e.target.closest('button')) return;
        if (e.touches.length === 1) {
            isDragging = true;
            lastClientX = e.touches[0].clientX;
            lastClientY = e.touches[0].clientY;
        } else if (e.touches.length === 2) {
            isDragging = false;
            lastPinchDist = Math.hypot(
                e.touches[0].clientX - e.touches[1].clientX,
                e.touches[0].clientY - e.touches[1].clientY
            );
        }
    }, { passive: true });

    container.addEventListener('touchmove', (e) => {
        if (minimapZoomLevel !== 3) return;
        if (e.target.closest('button')) return;
        e.preventDefault();

        if (e.touches.length === 1 && isDragging) {
            let dx = e.touches[0].clientX - lastClientX;
            let dy = e.touches[0].clientY - lastClientY;
            lastClientX = e.touches[0].clientX;
            lastClientY = e.touches[0].clientY;

            let baseScale = Math.min(mCanvas.width, mCanvas.height) / MAP_W;
            let scale = baseScale * fullMapZoom;
            if (scale > 0) {
                fullMapCenterX -= dx / scale;
                fullMapCenterY -= dy / scale;
                clampFullMapCenter();
            }
        } else if (e.touches.length === 2) {
            let dist = Math.hypot(
                e.touches[0].clientX - e.touches[1].clientX,
                e.touches[0].clientY - e.touches[1].clientY
            );
            if (lastPinchDist > 0 && dist > 0) {
                zoomFullMap(dist / lastPinchDist);
            }
            lastPinchDist = dist;
        }
    }, { passive: false });

    container.addEventListener('touchend', (e) => {
        if (e.touches.length === 0) {
            isDragging = false;
            lastPinchDist = 0;
        } else if (e.touches.length === 1) {
            isDragging = true;
            lastClientX = e.touches[0].clientX;
            lastClientY = e.touches[0].clientY;
            lastPinchDist = 0;
        }
    });
}

function toggleMinimapZoom() {
    let panel = document.getElementById('minimapPanel');
    let container = document.getElementById('minimapContainer');
    let fullControls = document.getElementById('fullMapControls');
    let toggleBtn = document.getElementById('minimapZoomToggleBtn');
    
    if (minimapZoomLevel === 1) {
        // 拡大
        minimapZoomLevel = 2;
        panel.style.width = '350px';
        panel.classList.remove('fixed', 'inset-3', 'sm:inset-8', 'z-50', 'bg-black', 'bg-opacity-90', 'scale-100');
        panel.classList.add('absolute', 'top-2', 'right-2', 'sm:top-4', 'sm:right-4', 'scale-90', 'sm:scale-100');
        container.classList.remove('flex-1', 'min-h-0', 'w-full');
        container.classList.add('aspect-square');
        container.style.cursor = 'pointer';
        if (fullControls) fullControls.classList.add('hidden');
        if (toggleBtn) toggleBtn.innerText = '全体';
        mCanvas.width = 320;
        mCanvas.height = 320;
    } else if (minimapZoomLevel === 2) {
        // 全体図 (画面中央・拡大縮小＆ドラッグ移動対応)
        minimapZoomLevel = 3;
        panel.style.width = 'auto';
        panel.classList.remove('absolute', 'top-2', 'right-2', 'sm:top-4', 'sm:right-4', 'scale-90');
        panel.classList.add('fixed', 'inset-3', 'sm:inset-8', 'z-50', 'bg-black', 'bg-opacity-90', 'scale-100');
        container.classList.remove('aspect-square');
        container.classList.add('flex-1', 'min-h-0', 'w-full');
        container.style.cursor = 'grab';
        if (fullControls) fullControls.classList.remove('hidden');
        if (toggleBtn) toggleBtn.innerText = '戻す';
        resetFullMapView();
        
        // パネル内のサイズに合わせてキャンバスを設定
        let rect = container.getBoundingClientRect();
        mCanvas.width = Math.max(100, Math.floor(rect.width));
        mCanvas.height = Math.max(100, Math.floor(rect.height));
    } else {
        // 標準に戻す
        minimapZoomLevel = 1;
        panel.classList.remove('fixed', 'inset-3', 'sm:inset-8', 'z-50', 'bg-black', 'bg-opacity-90', 'scale-100');
        panel.classList.add('absolute', 'top-2', 'right-2', 'sm:top-4', 'sm:right-4', 'scale-90', 'sm:scale-100');
        panel.style.width = '200px';
        container.classList.remove('flex-1', 'min-h-0', 'w-full');
        container.classList.add('aspect-square');
        container.style.cursor = 'pointer';
        if (fullControls) fullControls.classList.add('hidden');
        if (toggleBtn) toggleBtn.innerText = '拡大';
        mCanvas.width = 160;
        mCanvas.height = 160;
    }
}

// --- 描画処理 ---
function drawGame() {
    let now = Date.now();

    // カメラのシェイク適用
    let currentCamX = camX + (Math.random() - 0.5) * screenShake;
    let currentCamY = camY + (Math.random() - 0.5) * screenShake;

    // 背景クリア
    ctx.fillStyle = '#090607';
    ctx.fillRect(0, 0, canvas.width, canvas.height);

    // 描画範囲の計算
    let sCol = Math.max(0, Math.floor(currentCamX / TS) - 1);
    let eCol = Math.min(MAP_W, Math.ceil((currentCamX + canvas.width) / TS) + 1);
    let sRow = Math.max(0, Math.floor(currentCamY / TS) - 1);
    let eRow = Math.min(MAP_H, Math.ceil((currentCamY + canvas.height) / TS) + 1);

    // ピクセルアート風ドットを描画するヘルパー (16x16グリッド基準, 1ドット=3px, タイル外へのはみ出し防止付き)
    const P = 3;
    const drawPixel = (bx, by, gx, gy, gw, gh, color) => {
        const x1 = Math.max(0, gx);
        const y1 = Math.max(0, gy);
        const x2 = Math.min(16, gx + gw);
        const y2 = Math.min(16, gy + gh);
        if (x2 <= x1 || y2 <= y1) return;
        ctx.fillStyle = color;
        ctx.fillRect(bx + x1 * P, by + y1 * P, (x2 - x1) * P, (y2 - y1) * P);
    };

    // 同じグループ（シームレスに繋げるタイル）判定ヘルパー
    const isSameGroup = (t1, t2) => {
        if (t1 === t2) return true;
        if ((t1 === TILE.DIRT || t1 === TILE.BOMB) && (t2 === TILE.DIRT || t2 === TILE.BOMB)) return true;
        return false;
    };

        const isSolidWall = (t) => {
        return t === TILE.DIRT || t === TILE.BOMB || t === TILE.BOMB_ACTIVE || t === TILE.DEEP_DIRT ||
               t === TILE.ROCK || t === TILE.IRON || t === TILE.MAZE_BRICK || t === TILE.MAZE_BLOCK ||
               t === TILE.MUD || t === TILE.ASH;
    };

    // 1パス目: 地面と壁の描画（コアキーパー風2.5D立体ピクセルアート）
    for(let y = sRow; y < eRow; y++) {
        for(let x = sCol; x < eCol; x++) {
            let px = Math.floor(x * TS - currentCamX);
            let py = Math.floor(y * TS - currentCamY);

            // 未探索部分は真っ黒にして描画をスキップ
            if (!explored[y] || !explored[y][x]) {
                ctx.fillStyle = '#050304';
                ctx.fillRect(px, py, TS + 1, TS + 1);
                continue;
            }

            let tile = map[y][x];
            if (tile === TILE.VOID) {
                ctx.fillStyle = '#030203';
                ctx.fillRect(px, py, TS + 1, TS + 1);
                continue;
            }
            let hash = ((x * 374761393 + y * 668265263) ^ (x * y)) >>> 0;

            let tTop = (y > 0) ? map[y-1][x] : tile;
            let tBottom = (y < MAP_H - 1) ? map[y+1][x] : tile;
            let tLeft = (x > 0) ? map[y][x-1] : tile;
            let tRight = (x < MAP_W - 1) ? map[y][x+1] : tile;
            let tTopLeft = (y > 0 && x > 0) ? map[y-1][x-1] : tile;
            let tTopRight = (y > 0 && x < MAP_W - 1) ? map[y-1][x+1] : tile;
            let tBottomLeft = (y < MAP_H - 1 && x > 0) ? map[y+1][x-1] : tile;
            let tBottomRight = (y < MAP_H - 1 && x < MAP_W - 1) ? map[y+1][x+1] : tile;

            // --- ベースの床描画（コアキーパー風：赤茶と砂色が溶け合う高密度ピクセル地面） ---
            if (isSolidWall(tile)) {
                // 壁ブロックの場合は下に茶色い砂粒を描かず、各ブロック本来のベース色で塗る
                ctx.fillStyle = (tile === TILE.ASH) ? '#474b52' :
                                (tile === TILE.ROCK) ? '#464e57' :
                                (tile === TILE.IRON) ? '#2b3847' :
                                (tile === TILE.MAZE_BRICK) ? '#1e2736' :
                                (tile === TILE.MAZE_BLOCK) ? '#421f52' :
                                (tile === TILE.DEEP_DIRT) ? '#2b1815' :
                                (tile === TILE.MUD) ? '#261611' : '#683f31';
                ctx.fillRect(px, py, TS + 1, TS + 1);
            } else if (tile === TILE.ASH_FLOOR || tile === TILE.WITHERED_TREE) {
                // 廃離の森の灰色の地面（細かい灰の粒と吹き溜まり）
                ctx.fillStyle = '#474b52';
                ctx.fillRect(px, py, TS + 1, TS + 1);
                let ashWave = Math.sin(x * 0.45 + y * 0.3) + Math.cos(x * 0.25 - y * 0.5);
                if (ashWave > 0.4) {
                    ctx.fillStyle = '#555a63';
                    ctx.fillRect(px, py, TS + 1, TS + 1);
                } else if (ashWave < -0.5) {
                    ctx.fillStyle = '#3b3e45';
                    ctx.fillRect(px, py, TS + 1, TS + 1);
                }
                for (let g = 0; g < 10; g++) {
                    let h2 = ((hash ^ (g * 2654435761)) >>> 0);
                    let gx = h2 % 16;
                    let gy = (h2 >> 4) % 16;
                    let col = (g % 3 === 0) ? '#6e7580' : (g % 3 === 1 ? '#2f3238' : '#5d636e');
                    drawPixel(px, py, gx, gy, (g % 4 === 0 ? 2 : 1), 1, col);
                }

                for (let g = 0; g < 10; g++) {
                    let h2 = ((hash ^ (g * 2654435761)) >>> 0);
                    let gx = h2 % 16;
                    let gy = (h2 >> 4) % 16;
                    let col = (g % 3 === 0) ? '#6e7580' : (g % 3 === 1 ? '#2f3238' : '#5d636e');
                    drawPixel(px, py, gx, gy, (g % 4 === 0 ? 2 : 1), 1, col);
                }
            } else if (tile === TILE.FOREST_FLOOR || tile === TILE.FOREST_TREE) {
                // 森林の緑色の地面（深い苔色と草のグラデーション模様）
                ctx.fillStyle = '#2d5a27';
                ctx.fillRect(px, py, TS + 1, TS + 1);
                let grassWave = Math.sin(x * 0.4 + y * 0.35) + Math.cos(x * 0.3 - y * 0.45);
                if (grassWave > 0.35) {
                    ctx.fillStyle = '#386e30';
                    ctx.fillRect(px, py, TS + 1, TS + 1);
                } else if (grassWave < -0.45) {
                    ctx.fillStyle = '#23471e';
                    ctx.fillRect(px, py, TS + 1, TS + 1);
                }
                for (let g = 0; g < 10; g++) {
                    let h2 = ((hash ^ (g * 2654435761)) >>> 0);
                    let gx = h2 % 16;
                    let gy = (h2 >> 4) % 16;
                    let col = (g % 3 === 0) ? '#4caf50' : (g % 3 === 1 ? '#1b3b17' : '#66bb6a');
                    drawPixel(px, py, gx, gy, 1, (g % 3 === 0 ? 2 : 1), col);
                }        
            } else {
                // 連続波ノイズで赤茶の土と明るい黄土・砂地のゾーンを滑らかに生成
                let zoneNoise = Math.sin(x * 0.38 + y * 0.27) + Math.cos(x * 0.23 - y * 0.41) + Math.sin((x + y) * 0.19) * 0.6;
                
                // ベース色（中間色の赤茶砂地）
                ctx.fillStyle = '#683f31';
                ctx.fillRect(px, py, TS + 1, TS + 1);

                // 大きな地形の濃淡パッチ（明るい砂地ゾーン / 濃い赤茶ゾーン）
                if (zoneNoise > 0.65) {
                    ctx.fillStyle = '#7d513e'; // 明るい黄土・砂色エリア
                    ctx.fillRect(px, py, TS + 1, TS + 1);
                } else if (zoneNoise < -0.65) {
                    ctx.fillStyle = '#532f24'; // 濃い赤茶土エリア
                    ctx.fillRect(px, py, TS + 1, TS + 1);
                }

                // 境界をぼかす有機的なピクセルクラスター（8個の中間パッチ）
                const patchColors = ['#593327', '#754938', '#855843', '#4b291f', '#7d503d'];
                for (let pIdx = 0; pIdx < 8; pIdx++) {
                    let ph = ((hash ^ (pIdx * 1597334677)) >>> 0);
                    let gx = ph % 14;
                    let gy = (ph >> 5) % 14;
                    let gw = 2 + ((ph >> 10) % 3);
                    let gh = 2 + ((ph >> 13) % 2);
                    drawPixel(px, py, gx, gy, gw, gh, patchColors[(ph >> 16) % patchColors.length]);
                }

                // 高密度な1ドット砂粒・小石・ハイライト粒（1タイルあたり18粒描画）
                const grainColors = [
                    '#96684f', // 明るい砂粒ハイライト
                    '#a8795e', // キラッと光る砂粒
                    '#3d2018', // 濃い小石の影
                    '#4a271d', // 暗めの土粒
                    '#875842'  // 中間砂粒
                ];
                for (let g = 0; g < 18; g++) {
                    let gh = ((hash ^ (g * 2246822519)) >>> 0);
                    let gx = gh % 16;
                    let gy = (gh >> 4) % 16;
                    let cIdx = (gh >> 8) % grainColors.length;
                    // 明るい砂地ゾーンでは明るい砂粒を増やす
                    if (zoneNoise > 0.3 && cIdx === 2) cIdx = 0;
                    drawPixel(px, py, gx, gy, 1, 1, grainColors[cIdx]);
                }

                // たまに2x1の平たい小石を描画
                if ((hash % 3) === 0) {
                    let sx = (hash >> 3) % 13 + 1;
                    let sy = (hash >> 7) % 13 + 1;
                    drawPixel(px, py, sx, sy, 2, 1, '#3b1e16');
                    drawPixel(px, py, sx, sy - 1, 2, 1, '#8c5d46');
                }
            }

            // 壁のすぐ下の通路に落ちる濃い影（画像のような立体ドロップシャドウ）
            if (!isSolidWall(tile) && tile !== TILE.WATER && tile !== TILE.SWAMP_WATER && tile !== TILE.CHASM) {
                if (tBottom === TILE.VOID) {
                    ctx.fillStyle = 'rgba(0, 0, 0, 0.75)';
                    ctx.fillRect(px, py + TS - 8, TS + 1, 8);
                }
                if (tTop === TILE.VOID) {
                    ctx.fillStyle = 'rgba(0, 0, 0, 0.75)';
                    ctx.fillRect(px, py, TS + 1, 8);
                }
                if (tLeft === TILE.VOID) {
                    ctx.fillStyle = 'rgba(0, 0, 0, 0.75)';
                    ctx.fillRect(px, py, 8, TS + 1);
                }
                if (tRight === TILE.VOID) {
                    ctx.fillStyle = 'rgba(0, 0, 0, 0.75)';
                    ctx.fillRect(px + TS - 8, py, 8, TS + 1);
                }
                if (isSolidWall(tTop)) {
                    ctx.fillStyle = 'rgba(15, 7, 5, 0.62)';
                    ctx.fillRect(px, py, TS + 1, 12);
                    ctx.fillStyle = 'rgba(15, 7, 5, 0.30)';
                    ctx.fillRect(px, py + 12, TS + 1, 8);
                }
                if (isSolidWall(tLeft)) {
                    ctx.fillStyle = 'rgba(15, 7, 5, 0.38)';
                    ctx.fillRect(px, py, 9, TS + 1);
                }
                if (isSolidWall(tRight)) {
                    ctx.fillStyle = 'rgba(15, 7, 5, 0.28)';
                    ctx.fillRect(px + TS - 7, py, 7, TS + 1);
                }
                if (isSolidWall(tTopLeft) && !isSolidWall(tTop) && !isSolidWall(tLeft)) {
                    ctx.fillStyle = 'rgba(15, 7, 5, 0.35)';
                    ctx.fillRect(px, py, 9, 9);
                }
                if (isSolidWall(tTopRight) && !isSolidWall(tTop) && !isSolidWall(tRight)) {
                    ctx.fillStyle = 'rgba(15, 7, 5, 0.30)';
                    ctx.fillRect(px + TS - 9, py, 9, 9);
                }
            }

            // --- 各タイルの高精細ピクセル描画 ---
            if (tile === TILE.ASH_FLOOR) {
                if ((hash % 4) === 0) {
                    drawPixel(px, py, 4, 6, 5, 2, '#565b63');
                    drawPixel(px, py, 5, 7, 3, 1, '#6f7580');
                }
            } else if (tile === TILE.WITHERED_TREE) {
                ctx.fillStyle = 'rgba(0, 0, 0, 0.45)';
                ctx.beginPath(); ctx.ellipse(px + TS/2, py + TS - 5, 16, 6, 0, 0, Math.PI*2); ctx.fill();
                drawPixel(px, py, 6, 5, 4, 9, '#78909c');
                drawPixel(px, py, 7, 5, 2, 9, '#cfd8dc');
                drawPixel(px, py, 5, 13, 6, 2, '#607d8b');
                drawPixel(px, py, 3, 6, 3, 2, '#b0bec5');
                drawPixel(px, py, 2, 4, 2, 2, '#cfd8dc');
                drawPixel(px, py, 10, 5, 3, 2, '#b0bec5');
                drawPixel(px, py, 12, 3, 2, 2, '#cfd8dc');
                drawPixel(px, py, 7, 2, 2, 3, '#eceff1');

                if (blockHp[y][x] < 3) {
                    drawPixel(px, py, 6, 8, 4, 1, '#111');
                }
            } else if (isSolidWall(tile)) {
                // 画像のテラコッタ赤茶土壁を忠実に再現したカラーパレット
                let pal = {
                    top: '#a84b32', topLight: '#cc6447', topDark: '#853823',
                    cliff: '#5e2517', cliffDark: '#38130b', speck: '#e07b5c'
                };
                if (tile === TILE.DEEP_DIRT) {
                    pal = { top: '#472925', topLight: '#633b35', topDark: '#301a17', cliff: '#21110f', cliffDark: '#120807', speck: '#824f47' };
                } else if (tile === TILE.ROCK) {
                    pal = { top: '#788490', topLight: '#a0aebc', topDark: '#57626d', cliff: '#3e4650', cliffDark: '#242930', speck: '#cad6e0' };
                } else if (tile === TILE.IRON) {
                    pal = { top: '#485e75', topLight: '#6f8ca8', topDark: '#303f4f', cliff: '#212c38', cliffDark: '#131a22', speck: '#f1f5f9' };
                } else if (tile === TILE.MAZE_BRICK) {
                    pal = { top: '#354359', topLight: '#586c8f', topDark: '#222c3b', cliff: '#18202b', cliffDark: '#0e121a', speck: '#c084fc' };
                } else if (tile === TILE.MAZE_BLOCK) {
                    pal = { top: '#72378a', topLight: '#ad6ec7', topDark: '#4f2561', cliff: '#391a46', cliffDark: '#210e29', speck: '#f5eef8' };
                } else if (tile === TILE.MUD) {
                    pal = { top: '#523229', topLight: '#6e4539', topDark: '#38211b', cliff: '#261611', cliffDark: '#140b08', speck: '#8a5a4d' };
                } else if (tile === TILE.ASH) {
                    pal = { top: '#919ba6', topLight: '#bec8d2', topDark: '#6c757f', cliff: '#4c535b', cliffDark: '#2d3238', speck: '#f1f3f5' };
                }

                let sameTop = isSameGroup(tile, tTop);
                let sameBottom = isSameGroup(tile, tBottom);
                let sameLeft = isSameGroup(tile, tLeft);
                let sameRight = isSameGroup(tile, tRight);

                // 下が通路なら手前14pxを垂直の崖面として描画
                let hasCliff = !sameBottom;
                let cliffH = hasCliff ? 14 : 0;
                let topH = TS - cliffH;

                // 1. 天面（上面）のベース
                ctx.fillStyle = pal.top;
                ctx.fillRect(px, py, TS + 1, topH + 1);

                // 画像のような有機的なテクスチャ（天面内のやわらかな濃淡パッチ）
                drawPixel(px, py, (hash % 9) + 2, ((hash >> 4) % 5) + 2, 5, 3, pal.topDark);
                drawPixel(px, py, ((hash >> 8) % 9) + 2, ((hash >> 12) % 5) + 2, 4, 2, pal.topLight);
                if ((hash % 2) === 0) {
                    drawPixel(px, py, ((hash >> 16) % 11) + 2, ((hash >> 20) % 6) + 2, 1, 1, pal.speck);
                }

                // 鉄鉱石・遺跡レンガの固有ピクセル装飾
                if (tile === TILE.IRON) {
                    drawPixel(px, py, 3, 3, 4, 2, '#94a3b8');
                    drawPixel(px, py, 4, 3, 2, 1, '#ffffff');
                    drawPixel(px, py, 9, 6, 4, 2, '#94a3b8');
                    drawPixel(px, py, 10, 6, 2, 1, '#ffffff');
                } else if (tile === TILE.MAZE_BRICK) {
                    drawPixel(px, py, 7, 4, 2, 2, '#a855f7');
                    drawPixel(px, py, 3, 4, 1, 1, '#e040fb');
                    drawPixel(px, py, 12, 4, 1, 1, '#e040fb');
                }

                // 画像特有の「丸みを帯びた島状のオートタイル輪郭」
                const floorBg = (tile === TILE.ASH) ? '#474b52' :
                                (tile === TILE.ROCK) ? '#464e57' :
                                (tile === TILE.IRON) ? '#2b3847' :
                                (tile === TILE.MAZE_BRICK) ? '#1e2736' :
                                (tile === TILE.MAZE_BLOCK) ? '#421f52' :
                                (tile === TILE.DEEP_DIRT) ? '#2b1815' : '#683f31';
                if (!sameTop) {
                    ctx.fillStyle = pal.topLight;
                    ctx.fillRect(px, py, TS + 1, 4);
                    // 上の左右角が通路ならピクセル階段状に丸く削る
                    if (!sameLeft) {
                        ctx.fillStyle = floorBg;
                        ctx.fillRect(px, py, 6, 6);
                        ctx.fillRect(px, py, 9, 3);
                        ctx.fillRect(px, py, 3, 9);
                        ctx.fillStyle = pal.topLight;
                        ctx.fillRect(px + 6, py + 3, 3, 3);
                        ctx.fillRect(px + 3, py + 6, 3, 3);
                    }
                    if (!sameRight) {
                        ctx.fillStyle = floorBg;
                        ctx.fillRect(px + TS - 6, py, 6, 6);
                        ctx.fillRect(px + TS - 9, py, 9, 3);
                        ctx.fillRect(px + TS - 3, py, 3, 9);
                        ctx.fillStyle = pal.topLight;
                        ctx.fillRect(px + TS - 9, py + 3, 3, 3);
                        ctx.fillRect(px + TS - 6, py + 6, 3, 3);
                    }
                }
                if (!sameLeft) {
                    ctx.fillStyle = pal.topLight;
                    ctx.fillRect(px, py + (!sameTop ? 6 : 0), 3, topH - (!sameTop ? 6 : 0));
                }
                if (!sameRight) {
                ctx.fillStyle = pal.topDark;
                ctx.fillRect(px + TS - 3, py + (!sameTop ? 6 : 0), 3, topH - (!sameTop ? 6 : 0));
            }
            // 内角（L字に凹んだ部分）のピクセルハイライト
                if (sameTop && sameLeft && !isSameGroup(tile, tTopLeft)) {
                    drawPixel(px, py, 0, 0, 2, 2, pal.topLight);
                }
                if (sameTop && sameRight && !isSameGroup(tile, tTopRight)) {
                    drawPixel(px, py, 14, 0, 2, 2, pal.topLight);
                }

                // 2. 手前の断崖面（2.5D立体壁面）
                if (hasCliff) {
                    // 天面と崖面の境界エッジ（明るい縁取り）
                    ctx.fillStyle = pal.topLight;
                    ctx.fillRect(px, py + topH - 2, TS + 1, 2);

                    // 崖の側面本体
                    ctx.fillStyle = pal.cliff;
                    ctx.fillRect(px, py + topH, TS + 1, cliffH);

                    // 崖の左右下角も丸める
                    if (!sameLeft) {
                        ctx.fillStyle = floorBg;
                        ctx.fillRect(px, py + TS - 4, 4, 4);
                        ctx.fillStyle = pal.cliffDark;
                        ctx.fillRect(px + 4, py + topH, 3, cliffH);
                    }
                    if (!sameRight) {
                        ctx.fillStyle = floorBg;
                        ctx.fillRect(px + TS - 4, py + TS - 4, 4, 4);
                        ctx.fillStyle = pal.cliffDark;
                        ctx.fillRect(px + TS - 7, py + topH, 3, cliffH);
                    }

                    // 崖面の縦の岩肌・亀裂テクスチャ（画像左下の崖のような縦筋）
                    ctx.fillStyle = pal.cliffDark;
                    ctx.fillRect(px + 6, py + topH + 2, 4, cliffH - 2);
                    ctx.fillRect(px + 18, py + topH + 3, 6, cliffH - 3);
                    ctx.fillRect(px + 33, py + topH + 2, 5, cliffH - 2);

                    // 崖の接地影
                    ctx.fillStyle = 'rgba(0, 0, 0, 0.7)';
                    ctx.fillRect(px + (!sameLeft ? 4 : 0), py + TS - 3, TS + 1 - (!sameLeft ? 4 : 0) - (!sameRight ? 4 : 0), 3);
                }

                // ひび割れ（採掘ダメージ表現）
                let maxH = (tile === TILE.DEEP_DIRT) ? 10 : (tile === TILE.IRON ? 8 : (tile === TILE.ROCK || tile === TILE.MAZE_BLOCK ? 5 : (tile === TILE.MUD ? 2 : (tile === TILE.ASH ? 1 : 3))));
                let hpRatio = blockHp[y][x] / maxH;
                if (hpRatio < 1.0 && maxH > 1) {
                    drawPixel(px, py, 7, 5, 2, 2, '#111');
                    drawPixel(px, py, 5, 3, 2, 2, '#111');
                    if (hpRatio < 0.5) {
                        drawPixel(px, py, 9, 7, 2, 2, '#111');
                        drawPixel(px, py, 6, 8, 2, 1, '#111');
                    }
                }
            } else if (tile === TILE.HAY) {
                ctx.fillStyle = '#c8961e';
                ctx.fillRect(px, py, TS + 1, TS + 1);
                drawPixel(px, py, 2, 3, 6, 2, '#f1c40f');
                drawPixel(px, py, 8, 9, 6, 2, '#f1c40f');
                drawPixel(px, py, 3, 12, 5, 1, '#8c6b12');
                drawPixel(px, py, 9, 4, 5, 1, '#8c6b12');
            } else if (tile === TILE.ROOT) {
                // コアキーパー風の太く絡み合うピクセル木の根（周囲の壁色に馴染むベース）
                ctx.fillStyle = '#5c362c';
                ctx.fillRect(px, py, TS + 1, TS + 1);
                drawPixel(px, py, 1, 1, 14, 14, '#472820');
                drawPixel(px, py, 0, 5, 16, 4, '#24110b');
                drawPixel(px, py, 6, 0, 4, 16, '#24110b');
                drawPixel(px, py, 0, 4, 16, 3, '#8c5b4a');
                drawPixel(px, py, 0, 4, 16, 1, '#bfa69e');
                drawPixel(px, py, 6, 0, 3, 16, '#7a4d3e');
                drawPixel(px, py, 6, 0, 1, 16, '#ad9188');
                drawPixel(px, py, 2, 8, 3, 4, '#5e3b30');
                drawPixel(px, py, 11, 10, 3, 5, '#5e3b30');
                drawPixel(px, py, 10, 2, 4, 2, '#8c5b4a');

                if (blockHp[y][x] < 2) {
                    drawPixel(px, py, 6, 6, 4, 2, '#111');
                }
            } else if (tile === TILE.WORKBENCH || tile === TILE.WORKBENCH_ROCK || tile === TILE.WORKBENCH_SWAMP || tile === TILE.WORKBENCH_WITHERED) {
                let baseColor = '#a0522d';
                let topColor = '#cd853f';
                let shadowColor = '#5c2c0e';
                let legColor = '#6e2c00';
                if (tile === TILE.WORKBENCH_ROCK) { baseColor = '#7f8c8d'; topColor = '#95a5a6'; shadowColor = '#34495e'; legColor = '#2c3e50'; }
                if (tile === TILE.WORKBENCH_SWAMP) { baseColor = '#2e4033'; topColor = '#3e5c43'; shadowColor = '#1e2b22'; legColor = '#151e18'; }
                if (tile === TILE.WORKBENCH_WITHERED) { baseColor = '#64748b'; topColor = '#94a3b8'; shadowColor = '#334155'; legColor = '#1e293b'; }

                ctx.fillStyle = 'rgba(0,0,0,0.55)';
                ctx.beginPath(); ctx.ellipse(px+TS/2, py+TS-4, 20, 6, 0, 0, Math.PI*2); ctx.fill();
                drawPixel(px, py, 2, 3, 12, 7, baseColor);
                drawPixel(px, py, 2, 3, 12, 2, topColor);
                drawPixel(px, py, 3, 10, 2, 5, shadowColor);
                drawPixel(px, py, 11, 10, 2, 5, shadowColor);
                drawPixel(px, py, 4, 5, 4, 3, '#ecf0f1');
                drawPixel(px, py, 10, 4, 3, 2, '#bdc3c7');
                drawPixel(px, py, 11, 6, 1, 3, legColor);
            } else if (tile === TILE.ANVIL) {
                ctx.fillStyle = 'rgba(0,0,0,0.55)';
                ctx.beginPath(); ctx.ellipse(px+TS/2, py+TS-4, 16, 6, 0, 0, Math.PI*2); ctx.fill();
                // 鉄の塊っぽい描画
                drawPixel(px, py, 4, 10, 8, 4, '#1e293b');
                drawPixel(px, py, 6, 6, 4, 4, '#334155');
                drawPixel(px, py, 3, 2, 10, 4, '#475569');
                drawPixel(px, py, 3, 2, 10, 1, '#94a3b8');
            } else if (tile >= TILE.DIRT_CHEST && tile <= TILE.MEDUSA_CHEST) {
                let baseColor, trimColor, lockColor = '#f1c40f';
                if (tile === TILE.DIRT_CHEST) { baseColor = '#6d4c41'; trimColor = '#3e2723'; }
                else if (tile === TILE.WOOD_CHEST) { baseColor = '#b45309'; trimColor = '#78350f'; }
                else if (tile === TILE.ROCK_CHEST) { baseColor = '#64748b'; trimColor = '#334155'; }
                else if (tile === TILE.IRON_CHEST) { baseColor = '#cbd5e1'; trimColor = '#64748b'; lockColor = '#38bdf8'; }
                else if (tile === TILE.MUD_CHEST) { baseColor = '#5d4037'; trimColor = '#27140e'; lockColor = '#a1887f'; }
                else if (tile === TILE.ASH_CHEST) { baseColor = '#94a3b8'; trimColor = '#475569'; lockColor = '#e2e8f0'; }
                else if (tile === TILE.WITHERED_CHEST) { baseColor = '#475569'; trimColor = '#1e293b'; lockColor = '#94a3b8'; }
                else if (tile === TILE.MAZE_CHEST) { baseColor = '#a855f7'; trimColor = '#4c1d95'; lockColor = '#f43f5e'; }
                else if (tile === TILE.GEERA_CHEST) { baseColor = '#d35400'; trimColor = '#873600'; lockColor = '#f39c12'; }
                else if (tile === TILE.MEDUSA_CHEST) { baseColor = '#117a65'; trimColor = '#0b5345'; lockColor = '#2ecc71'; }

                ctx.fillStyle = 'rgba(0,0,0,0.55)';
                ctx.beginPath(); ctx.ellipse(px+TS/2, py+TS-6, 18, 7, 0, 0, Math.PI*2); ctx.fill();
                
                ctx.fillStyle = trimColor;
                ctx.fillRect(px + 6, py + TS/2 - 4, TS - 12, TS/2 + 2);
                ctx.fillStyle = baseColor;
                ctx.fillRect(px + 8, py + TS/2 - 2, TS - 16, TS/2 - 2);
                
                ctx.fillStyle = lockColor;
                ctx.fillRect(px + TS/2 - 3, py + TS/2 + 2, 6, 4);

                // 泥チェストや灰チェストの脈動・点滅エフェクト
                if (tile === TILE.MUD_CHEST || tile === TILE.ASH_CHEST) {
                    let chest = placedChests[`${x},${y}`];
                    if (chest && chest.timer) {
                        let ratio = chest.timer / 60000;
                        if (tile === TILE.MUD_CHEST) {
                            let pulse = Math.sin(now/150) * (1 - ratio) * 2;
                            ctx.fillStyle = 'rgba(139, 69, 19, 0.4)';
                            ctx.fillRect(px+8, py+TS/2-2, TS-16, TS/2-2);
                        } else if (tile === TILE.ASH_CHEST) {
                            if (chest.timer < 3000 && Math.floor(now/150)%2===0) {
                                ctx.fillStyle = 'rgba(255, 100, 100, 0.6)';
                                ctx.fillRect(px+8, py+TS/2-2, TS-16, TS/2-2);
                            }
                        }
                    }
                }
            } else if (tile === TILE.MAZE_UNREAL) {
                let auraRadius = Math.max(4.1, TS * 1.1);
                let auraGrad = ctx.createRadialGradient(px+TS/2, py+TS/2, 4, px+TS/2, py+TS/2, auraRadius);
                auraGrad.addColorStop(0, 'rgba(213, 0, 249, 0.55)');
                auraGrad.addColorStop(1, 'rgba(26, 35, 126, 0)');
                ctx.fillStyle = auraGrad;
                ctx.fillRect(px - TS/2, py - TS/2, TS*2, TS*2);

                drawPixel(px, py, 2, 2, 12, 12, '#1a237e');
                drawPixel(px, py, 3, 3, 10, 10, '#3949ab');
                let pulse = Math.sin(Date.now() / 200) * 3;
                ctx.fillStyle = '#e040fb';
                ctx.beginPath(); ctx.arc(px+TS/2, py+TS/2, 8 + pulse, 0, Math.PI*2); ctx.fill();
                ctx.fillStyle = '#ffffff';
                ctx.beginPath(); ctx.arc(px+TS/2, py+TS/2, 3, 0, Math.PI*2); ctx.fill();
            } else if (tile === TILE.WATER || tile === TILE.SWAMP_WATER) {
                ctx.fillStyle = tile === TILE.SWAMP_WATER ? '#145a4c' : '#1b4f72';
                ctx.fillRect(px, py, TS + 1, TS + 1);

                let shoreCol = tile === TILE.SWAMP_WATER ? '#27ae60' : '#5dade2';
                let deepCol = tile === TILE.SWAMP_WATER ? '#0e3d33' : '#154360';
                if (tTop !== tile) {
                    ctx.fillStyle = deepCol; ctx.fillRect(px, py, TS + 1, 5);
                    ctx.fillStyle = shoreCol; ctx.fillRect(px, py + 5, TS + 1, 2);
                }
                if (tBottom !== tile) { ctx.fillStyle = shoreCol; ctx.fillRect(px, py + TS - 3, TS + 1, 3); }
                if (tLeft !== tile) { ctx.fillStyle = shoreCol; ctx.fillRect(px, py, 3, TS + 1); }
                if (tRight !== tile) { ctx.fillStyle = shoreCol; ctx.fillRect(px + TS - 3, py, 3, TS + 1); }

                let waveShift = Math.floor((Date.now() / 350 + x + y) % 4);
                drawPixel(px, py, 3 + waveShift, 5, 3, 1, tile === TILE.SWAMP_WATER ? '#58d68d' : '#aed6f1');
                drawPixel(px, py, 9 - waveShift, 11, 3, 1, tile === TILE.SWAMP_WATER ? '#58d68d' : '#aed6f1');
            } else if (tile === TILE.CHASM) {
                ctx.fillStyle = '#050406';
                ctx.fillRect(px, py, TS + 1, TS + 1);
                ctx.fillStyle = '#331d17';
                if (tTop !== TILE.CHASM) ctx.fillRect(px, py, TS + 1, 6);
                if (tLeft !== TILE.CHASM) ctx.fillRect(px, py, 4, TS + 1);
                if (tRight !== TILE.CHASM) ctx.fillRect(px + TS - 4, py, 4, TS + 1);
            }
        }
    }

    // ターゲットカーソル（画像中央の水色の矢印のように目の前のマスを示す）
    let targetPx = player.px;
    let targetPy = player.py;
    if (player.dir === 'up') targetPy -= TS;
    else if (player.dir === 'down') targetPy += TS;
    else if (player.dir === 'left') targetPx -= TS;
    else if (player.dir === 'right') targetPx += TS;
    let tCol = Math.floor(targetPx / TS);
    let tRow = Math.floor(targetPy / TS);
    if (tCol >= 0 && tCol < MAP_W && tRow >= 0 && tRow < MAP_H) {
        let curScreenX = tCol * TS + TS / 2 - currentCamX;
        let curScreenY = tRow * TS + TS / 2 - currentCamY;
        let bob = Math.sin(Date.now() / 180) * 2;
        ctx.fillStyle = 'rgba(56, 189, 248, 0.85)';
        ctx.strokeStyle = '#0c4a6e';
        ctx.lineWidth = 1.5;
        ctx.beginPath();
        ctx.moveTo(curScreenX, curScreenY - 7 + bob);
        ctx.lineTo(curScreenX + 6, curScreenY + 3 + bob);
        ctx.lineTo(curScreenX + 2, curScreenY + 3 + bob);
        ctx.lineTo(curScreenX + 2, curScreenY + 8 + bob);
        ctx.lineTo(curScreenX - 2, curScreenY + 8 + bob);
        ctx.lineTo(curScreenX - 2, curScreenY + 3 + bob);
        ctx.lineTo(curScreenX - 6, curScreenY + 3 + bob);
        ctx.closePath();
        ctx.fill();
        ctx.stroke();
    }

    // 起動済み点滅爆弾の描画
    activeBombs.forEach(b => {
        if (!explored[b.y] || !explored[b.y][b.x]) return;
        let px = b.x * TS - currentCamX;
        let py = b.y * TS - currentCamY;
        let blinkRate = Math.max(50, b.timer / 10); 
        let isRed = Math.floor(now / blinkRate) % 2 === 0;
        
        ctx.fillStyle = isRed ? '#e74c3c' : '#8c4a32';
        ctx.fillRect(px, py, TS, TS);
        ctx.fillStyle = '#2b120b';
        ctx.fillRect(px, py + TS - 14, TS, 14);

        ctx.fillStyle = 'white';
        ctx.font = 'bold 16px Inter';
        ctx.textAlign = 'center';
        ctx.fillText(Math.ceil(b.timer/1000), px + TS/2, py + TS/2 + 4);
    });

    // 高精細ピクセルアート用ヘルパー関数群
    const drawTexturedRect = (x, y, w, h, baseColor, detailColor1, detailColor2, chance1 = 0.2, chance2 = 0.1, seed = 0) => {
        let drawX = w < 0 ? x + w : x;
        let drawW = Math.abs(w);
        ctx.fillStyle = baseColor;
        ctx.fillRect(drawX, y, drawW, h);
        for(let dy=0; dy<h; dy+=3) {
            for(let dx=0; dx<drawW; dx+=3) {
                let px = Math.floor((drawX + dx)/3);
                let py = Math.floor((y + dy)/3);
                let hash = (px * 89 + py * 41 + seed * 137) >>> 0;
                let r = (hash % 100) / 100;
                if(r < chance2) {
                    ctx.fillStyle = detailColor2;
                    ctx.fillRect(drawX + dx, y + dy, 3, 3);
                } else if(r < chance2 + chance1) {
                    ctx.fillStyle = detailColor1;
                    ctx.fillRect(drawX + dx, y + dy, 3, 3);
                }
            }
        }
    };

    const drawPixelCircle = (cx, cy, r, baseColor, detailColor1 = null, detailColor2 = null) => {
        for(let dy = -r; dy <= r; dy+=3) {
            for(let dx = -r; dx <= r; dx+=3) {
                if(dx*dx + dy*dy <= r*r) {
                    if (detailColor1 && detailColor2) {
                        let px = Math.floor((cx + dx)/3);
                        let py = Math.floor((cy + dy)/3);
                        let hash = (px * 89 + py * 41) >>> 0;
                        let rnd = (hash % 100) / 100;
                        if(rnd < 0.15) ctx.fillStyle = detailColor2;
                        else if(rnd < 0.35) ctx.fillStyle = detailColor1;
                        else ctx.fillStyle = baseColor;
                    } else {
                        ctx.fillStyle = baseColor;
                    }
                    ctx.fillRect(cx + dx, cy + dy, 3, 3);
                }
            }
        }
    };

    // 石像の描画
    statues.forEach(s => {
        if(map[s.y][s.x] !== TILE.FLOOR && map[s.y][s.x] !== TILE.HAY && map[s.y][s.x] !== TILE.ASH_FLOOR) return;
        if(!explored[s.y] || !explored[s.y][s.x]) return;

        let w = TS * 3;
        let h = TS * 3;
        let sx = (s.x - 1) * TS - currentCamX;
        let sy = (s.y - 1) * TS - currentCamY;
        let seed = s.x * 73 + s.y * 31; // 石像ごとに固有のシード値

        ctx.save();
        ctx.translate(sx, sy); // ローカル座標化！これで模様が固定される

        // ドロップシャドウ
        ctx.fillStyle = 'rgba(0,0,0,0.6)';
        ctx.beginPath(); ctx.ellipse(w/2, h - 12, w/2 - 6, 15, 0, 0, Math.PI*2); ctx.fill();
        
        // ベースの石 (周辺の岩石ブロック TILE.ROCK と同じパレットを使用)
        let rockTop = '#788490';
        let rockLight = '#a0aebc';
        let rockDark = '#57626d';
        let rockCliff = '#3e4650';
        let rockCliffDark = '#242930';

        // 形をより「自然な巨石・岩山」っぽくする。下段は広く、上に行くほど狭くする。
        // 土台部分 (崖面と天面)
        drawTexturedRect(12, h - 33, w - 24, 21, rockCliff, rockCliffDark, rockDark, 0.4, 0.3, seed);
        drawTexturedRect(12, h - 42, w - 24, 9, rockTop, rockLight, rockDark, 0.3, 0.1, seed + 1);
        
        // 土台の出っ張り（左右）- 左右の出っ張り幅もシードでランダム化
        let leftW = 12 + (seed % 3) * 3;
        let rightW = 12 + ((seed >> 2) % 3) * 3;
        
        drawTexturedRect(12 - leftW, h - 21, leftW + 3, 12, rockCliff, rockCliffDark, rockDark, 0.4, 0.2, seed + 2);
        drawTexturedRect(12 - leftW, h - 27, leftW + 3, 6, rockTop, rockLight, rockDark, 0.3, 0.1, seed + 3);
        
        drawTexturedRect(w - 15, h - 24, rightW + 3, 15, rockCliff, rockCliffDark, rockDark, 0.4, 0.2, seed + 4);
        drawTexturedRect(w - 15, h - 30, rightW + 3, 6, rockTop, rockLight, rockDark, 0.3, 0.1, seed + 5);

        // 中段（幅をシードでランダム化）
        let midW = w - 42 - ((seed >> 4) % 2) * 6; 
        let midX = (w - midW) / 2;
        drawTexturedRect(midX, h - 60, midW, 21, rockCliff, rockCliffDark, rockDark, 0.4, 0.3, seed + 6);
        drawTexturedRect(midX, h - 69, midW, 9, rockTop, rockLight, rockDark, 0.3, 0.1, seed + 7);

        // 上段（コアが浮く場所の下。幅をシードでランダム化）
        let topW = midW - 12 - ((seed >> 5) % 2) * 6;
        let topX = (w - topW) / 2;
        drawTexturedRect(topX, h - 84, topW, 18, rockCliff, rockCliffDark, rockDark, 0.4, 0.2, seed + 8);
        drawTexturedRect(topX, h - 93, topW, 9, rockTop, rockLight, rockDark, 0.3, 0.1, seed + 9);

        // ヒビ割れ（暗い線）- シードに基づいてランダムな位置・長さに刻む
        ctx.fillStyle = rockCliffDark;
        let crack1X = midX + 3 + (seed % (Math.floor(midW/3) - 2)) * 3;
        let crack1Y = h - 24 - ((seed >> 6) % 3) * 3;
        ctx.fillRect(crack1X, crack1Y, 3, 9 + ((seed >> 7) % 2)*3);
        ctx.fillRect(crack1X - 3, crack1Y + 3, 6, 3);
        
        let crack2X = topX + 3 + ((seed >> 2) % (Math.floor(topW/3) - 2)) * 3;
        let crack2Y = h - 54 - ((seed >> 8) % 3) * 3;
        ctx.fillRect(crack2X, crack2Y, 6, 6 + ((seed >> 9) % 2)*3);
        if (seed % 2 === 0) ctx.fillRect(crack2X + 6, crack2Y + 3, 3, 6);

        // 古代ルーンの彫り込み（発光）- かすれ・欠けをシードでランダムに表現
        ctx.shadowBlur = 12; ctx.shadowColor = s.color; ctx.fillStyle = s.color;
        // ルーンは中段の崖面に刻む
        for (let i = 0; i < 3; i++) {
            if (((seed >> (10 + i)) % 4) !== 0) { // たまにルーンが欠けて消える
                ctx.fillRect(w/2 - 9, h - 54 + i*6, 18, 3);
                ctx.fillRect(w/2 - 3, h - 54 + i*6, 6, 6);
            }
        }
        ctx.shadowBlur = 0; ctx.fillStyle = '#fff';
        for (let i = 0; i < 3; i++) {
             if (((seed >> (10 + i)) % 4) !== 0) {
                 ctx.fillRect(w/2 - 1, h - 53 + i*6, 3, 3);
             }
        }

        if (s.type === 'north') {
            // 炎のピクセルディテール
            for (let r=0; r<60; r++) {
                let px = w/2 - 30 + Math.random()*60;
                let py = h/2 - 45 + Math.random()*60;
                if (Math.hypot(px - (w/2), py - (h/2 - 15)) < 26) {
                    ctx.fillStyle = Math.random() > 0.4 ? '#e67e22' : (Math.random() > 0.5 ? '#d35400' : '#f1c40f');
                    ctx.fillRect(Math.floor(px/3)*3, Math.floor(py/3)*3, 3, 3);
                }
            }
        }

        // 浮遊するコアクリスタル（ピクセルアート風）
        let floatY = Math.floor(Math.sin(now / 300) * 6 / 3) * 3;
        let cx = Math.floor((w/2)/3)*3;
        let cy = Math.floor((36 + floatY)/3)*3;
        
        ctx.shadowBlur = 22; ctx.shadowColor = s.color;
        for(let dy=-21; dy<=21; dy+=3) {
            let wid = 21 - Math.abs(dy);
            for(let dx=-wid; dx<=wid; dx+=3) {
                if (dx > 0 && dx < wid - 3) ctx.fillStyle = 'rgba(255,255,255,0.4)';
                else if (dx < 0 && dx > -wid + 3) ctx.fillStyle = 'rgba(0,0,0,0.3)';
                else ctx.fillStyle = s.color;
                ctx.fillRect(cx + dx, cy + dy, 3, 3);
            }
        }
        ctx.shadowBlur = 0;
        ctx.fillStyle = '#ffffff';
        ctx.fillRect(cx - 3, cy - 6, 6, 12);
        
        if(s.type !== 'center' && player.activeStatues[s.type]) {
            ctx.fillStyle = s.color;
            ctx.globalAlpha = 0.55 + Math.sin(now/150)*0.3;
            for(let by = cy + 24; by < h - 60; by+=3) {
                let bw = 6 + Math.sin(by/10 + now/200)*3;
                ctx.fillRect(cx - Math.floor(bw/3)*3, by, Math.floor(bw/3)*6, 3);
            }
            ctx.globalAlpha = 1.0;
            ctx.fillStyle = '#fff';
            for(let i=0; i<4; i++) {
                let pY = h - 60 - ((now/20 + i*30) % (h - 80 - floatY));
                ctx.fillRect(cx - 3, Math.floor(pY/3)*3, 6, 6);
            }
        }
        
        ctx.restore();
    });

    // NPCの描画
    npcs.forEach(n => {
        let mapNx = Math.floor(n.x / TS);
        let mapNy = Math.floor(n.y / TS);
        if (!explored[mapNy] || !explored[mapNy][mapNx]) return;

        let nx = n.x - currentCamX;
        let ny = n.y - currentCamY;
        
        ctx.fillStyle = 'rgba(0,0,0,0.5)';
        ctx.beginPath(); ctx.ellipse(nx, ny + 14, 15, 6, 0, 0, Math.PI*2); ctx.fill();

        if (n.type === 'heiza') {
            ctx.save();
            ctx.translate(nx, ny);

            // ローブ背面
            for(let dy=-30; dy<12; dy+=3) {
                let bw = 24 + (dy > -10 ? (dy+10)*0.5 : 0);
                drawTexturedRect(Math.floor(-bw/2/3)*3, dy, Math.floor(bw/3)*3, 3, '#3a134a', '#4a235a', '#1a0526', 0.4, 0.2);
            }
            // ローブ前面
            for(let dy=-27; dy<12; dy+=3) {
                let bw = 18 + (dy > 0 ? dy*0.5 : 0);
                drawTexturedRect(Math.floor(-bw/2/3)*3, dy, Math.floor(bw/3)*3, 3, '#5b2c6f', '#7d3c98', '#4a235a', 0.4, 0.3);
            }
            
            // 金のベルト
            drawTexturedRect(-9, -3, 18, 3, '#b7950b', '#f1c40f', '#9a7d0a', 0.5, 0.1);
            ctx.fillStyle = '#f5b041'; ctx.fillRect(-3, -6, 6, 9);
            ctx.fillStyle = '#7d3c98'; ctx.fillRect(-1, -3, 3, 3);
            
            // 腕と袖
            drawTexturedRect(8, -6, 12, 12, '#5b2c6f', '#7d3c98', '#4a235a', 0.4, 0.2);
            
            // 杖
            drawTexturedRect(12, -36, 6, 48, '#3e2723', '#5d4037', '#261611', 0.5, 0.2);
            ctx.fillStyle = '#e67e22'; ctx.shadowBlur = 15; ctx.shadowColor = '#f39c12';
            drawPixelCircle(15, -36, 6, '#e67e22', '#f39c12', '#d35400');
            ctx.shadowBlur = 0; ctx.fillStyle = '#fff'; ctx.fillRect(12, -36, 6, 6);

            // フードと顔
            drawTexturedRect(-9, -21, 18, 15, '#4a235a', '#5b2c6f', '#2c1236', 0.4, 0.2); // フード奥
            ctx.fillStyle = '#f5cba7'; ctx.fillRect(-6, -18, 12, 12);
            ctx.fillStyle = 'rgba(0,0,0,0.4)'; ctx.fillRect(-6, -18, 12, 6);
            ctx.fillStyle = '#e040fb'; ctx.fillRect(-6, -15, 3, 3); ctx.fillRect(3, -15, 3, 3);

            // 白ヒゲ
            drawTexturedRect(-9, -6, 18, 6, '#ecf0f1', '#bdc3c7', '#ffffff', 0.4, 0.2);
            drawTexturedRect(-6, 0, 12, 6, '#ecf0f1', '#bdc3c7', '#ffffff', 0.4, 0.2);
            drawTexturedRect(-3, 6, 6, 6, '#ecf0f1', '#bdc3c7', '#ffffff', 0.4, 0.2);

            ctx.fillStyle = 'white'; ctx.font = 'bold 16px Inter'; ctx.textAlign = 'center'; ctx.fillText("?", 0, -45);
            ctx.restore();
        } else if (n.type === 'girieve') {
            ctx.save();
            ctx.translate(nx, ny);

            // リュック
            drawPixelCircle(-12, -6, 16, '#5c3a21', '#8b5a2b', '#3e2723');
            drawTexturedRect(-21, -3, 9, 9, '#3e2723', '#212121', '#4e342e', 0.3, 0.2);
            drawTexturedRect(-18, 0, 6, 6, '#795548', '#5d4037', '#8d6e63', 0.3, 0.2);
            
            // 寝袋
            drawTexturedRect(-21, -24, 18, 9, '#2e4053', '#34495e', '#17202a', 0.4, 0.2);
            ctx.fillStyle = '#17202a'; ctx.fillRect(-15, -24, 3, 9); ctx.fillRect(-9, -24, 3, 9);

            // ランタン
            let lanternSwing = Math.sin(now/200)*3;
            let lx = Math.floor((-24 + lanternSwing)/3)*3;
            drawTexturedRect(lx, -3, 6, 9, '#7f8c8d', '#95a5a6', '#546e7a', 0.4, 0.2);
            ctx.fillStyle = '#f1c40f'; ctx.fillRect(lx + 3, 0, 3, 3);
            for(let i=0; i<5; i++) {
                ctx.fillStyle = '#2c3e50';
                ctx.fillRect(lx + 3 + (i*3) - (-18), -6 + (i*1) - (-3), 3, 3); 
            }
            
            // 本体
            drawPixelCircle(0, -2, 17, '#0b5345', '#117a65', '#0e6251');
            drawTexturedRect(-9, 0, 18, 9, '#a9dfbf', '#76d7c4', '#d1f2eb', 0.4, 0.2); // お腹
            
            // 手足
            drawTexturedRect(-15, 9, 9, 6, '#117a65', '#1abc9c', '#0b5345', 0.4, 0.2);
            drawTexturedRect(6, 9, 9, 6, '#117a65', '#1abc9c', '#0b5345', 0.4, 0.2);
            
            // 目
            drawTexturedRect(-15, -21, 12, 12, '#117a65', '#1abc9c', '#0b5345', 0.3, 0.2);
            drawTexturedRect(3, -21, 12, 12, '#117a65', '#1abc9c', '#0b5345', 0.3, 0.2);
            ctx.fillStyle = '#f1c40f'; ctx.fillRect(-12, -18, 6, 6); ctx.fillRect(6, -18, 6, 6);
            ctx.fillStyle = '#000'; ctx.fillRect(-12, -18, 6, 3); ctx.fillRect(6, -18, 6, 3);
            ctx.fillStyle = '#fff'; ctx.fillRect(-9, -18, 3, 3); ctx.fillRect(9, -18, 3, 3);

            ctx.fillStyle = 'white'; ctx.font = 'bold 16px Inter'; ctx.textAlign = 'center'; ctx.fillText("!", 0, -32);
            ctx.restore();
        }
    });

    // ボス（守護者・ラスボス）の描画関数
    let drawBoss = (b) => {
        if(b.active && !b.dead) {
            let mapBx = Math.floor(b.x);
            let mapBy = Math.floor(b.y);
            if (mapBy >= 0 && mapBy < MAP_H && mapBx >= 0 && mapBx < MAP_W) {
                if (!explored[mapBy][mapBx]) return;
            }

            let bx = b.x * TS + TS/2 - currentCamX;
            let by = b.y * TS + TS/2 - currentCamY;
            
            if (b === finalBoss) {
                // マスターコア（高精細ピクセル曼荼羅）
                let corePulse = Math.floor(Math.sin(now/200)*2)*3;
                drawPixelCircle(bx, by, 36 + corePulse, 'rgba(168, 85, 247, 0.25)');
                drawPixelCircle(bx, by, 24, '#4c1d95', '#5b2c6f', '#3a134a');
                drawPixelCircle(bx, by, 18, b.color, '#7d3c98', '#2c1236');
                drawTexturedRect(bx - 6, by - 6, 12, 12, '#e040fb', '#f39c12', '#9b59b6', 0.5, 0.2);
                ctx.fillStyle = '#ffffff'; ctx.fillRect(bx - 3, by - 3, 6, 6);
                
                // 回転する衛星ドット
                for(let i=0; i<4; i++) {
                    let a = now/500 + i*Math.PI/2;
                    let satX = bx + Math.cos(a)*27;
                    let satY = by + Math.sin(a)*27;
                    drawTexturedRect(Math.floor(satX/3)*3, Math.floor(satY/3)*3, 9, 9, '#d8b4fe', '#e040fb', '#9b59b6', 0.4, 0.2);
                }
            } else if (b.id === 'medusa') {
                ctx.save();
                ctx.translate(bx, by);
                ctx.scale(b.facing, 1);
                
                // 足元の影
                ctx.fillStyle = 'rgba(0, 0, 0, 0.6)';
                ctx.beginPath(); ctx.ellipse(0, 28, 32, 12, 0, 0, Math.PI * 2); ctx.fill();

                // 蛇腹（ドット構築）
                let tW = Math.sin(now / 150) * 12;
                
                // 奥の尾
                for (let i=0; i<15; i++) {
                    let w = 24 - i*1.2;
                    let segX = -12 + tW*(i/15) + (i*1.5);
                    let segY = 6 + i*2;
                    drawTexturedRect(Math.floor((segX-w/2)/3)*3, Math.floor(segY/3)*3, Math.floor(w/3)*3, 6, '#07362a', '#0e6251', '#041f18', 0.4, 0.2);
                }
                
                // 手前の尾
                for (let i=0; i<18; i++) {
                    let w = 27 - i*1.2;
                    let segX = -6 + tW*(i/18) + (i*1.2);
                    let segY = 12 + i*2;
                    drawTexturedRect(Math.floor((segX-w/2)/3)*3, Math.floor(segY/3)*3, Math.floor(w/3)*3, 6, '#117a65', '#1abc9c', '#0b5345', 0.4, 0.3);
                }
                
                // 腹部（ハイライト）
                for (let i=0; i<10; i++) {
                    let segX = -12 + tW*(i/10) + (i*1.5);
                    let segY = 15 + i*2;
                    ctx.fillStyle = '#d4efdf';
                    ctx.fillRect(Math.floor(segX/3)*3, Math.floor(segY/3)*3, 6, 3);
                }

                // ドレス
                drawTexturedRect(-15, -15, 30, 24, '#0e6251', '#1abc9c', '#0b5345', 0.4, 0.2);
                drawTexturedRect(-15, -6, 30, 3, '#b7950b', '#f1c40f', '#9a7d0a', 0.5, 0.2);
                ctx.fillStyle = '#9b59b6'; ctx.fillRect(-6, -15, 12, 12);
                ctx.fillStyle = '#e040fb'; ctx.fillRect(-3, -12, 6, 6);

                // 頭部
                drawTexturedRect(-12, -33, 24, 18, '#d1f2eb', '#a3e4d7', '#76d7c4', 0.3, 0.1);
                ctx.fillStyle = '#641e16'; ctx.fillRect(-3, -18, 6, 3);
                
                // 蛇の髪
                for (let sIdx = -3; sIdx <= 3; sIdx++) {
                    let sx = sIdx * 5;
                    let sy = -33;
                    let wiggle = Math.sin(now / 120 + sIdx * 1.5) * 6;
                    
                    let col1 = sIdx % 2 === 0 ? '#27ae60' : '#1e8449';
                    let col2 = sIdx % 2 === 0 ? '#2ecc71' : '#229954';
                    
                    for (let j=0; j<5; j++) {
                        let hx = sx + (wiggle * j / 4);
                        let hy = sy - j*3;
                        ctx.fillStyle = j%2===0 ? col1 : col2;
                        ctx.fillRect(Math.floor(hx/3)*3, Math.floor(hy/3)*3, 3, 3);
                    }
                    
                    let headX = Math.floor((sx + wiggle)/3)*3;
                    let headY = Math.floor((sy - 18)/3)*3;
                    drawTexturedRect(headX - 3, headY - 3, 6, 6, col2, col1, '#117a65', 0.4, 0.2);
                    ctx.fillStyle = '#f1c40f'; ctx.fillRect(headX - 3, headY - 3, 3, 3); ctx.fillRect(headX + 3, headY - 3, 3, 3);
                }

                // 目
                let eyeGlow = (b.state === 'spread_prepare' || b.state === 'line_prepare' || b.ultReady) ? '#facc15' : '#e74c3c';
                ctx.shadowBlur = 12; ctx.shadowColor = eyeGlow; ctx.fillStyle = eyeGlow;
                ctx.fillRect(-6, -27, 6, 3); ctx.fillRect(6, -27, 6, 3);
                ctx.shadowBlur = 0; ctx.fillStyle = '#000';
                ctx.fillRect(-3, -27, 3, 3); ctx.fillRect(9, -27, 3, 3);

                ctx.restore();
            } else {
                ctx.save();
                ctx.translate(bx, by);
                ctx.scale(b.facing, 1);
                
                let isMoving = b.state === 'walk' || b.state === 'charge';
                if (b.state === 'stun') {
                    ctx.translate((Math.random() - 0.5) * 6, (Math.random() - 0.5) * 6);
                }

                let legAnim1 = isMoving ? Math.floor(Math.sin(now/100)*15/3)*3 : 0;
                let legAnim2 = isMoving ? Math.floor(Math.cos(now/100)*15/3)*3 : 0;

                ctx.fillStyle = 'rgba(0,0,0,0.6)';
                ctx.beginPath(); ctx.ellipse(0, 38, 48, 16, 0, 0, Math.PI*2); ctx.fill();

                // 奥の足
                drawTexturedRect(-39, 15, 12, 27 + legAnim1, '#2e1c15', '#3e2723', '#1a110e', 0.4, 0.2); 
                drawTexturedRect(3, 15, 12, 27 + legAnim2, '#2e1c15', '#3e2723', '#1a110e', 0.4, 0.2); 
                ctx.fillStyle = '#111'; ctx.fillRect(-39, 42 + legAnim1, 12, 6); ctx.fillRect(3, 42 + legAnim2, 12, 6);

                // 馬の胴体
                drawTexturedRect(-42, 3, 60, 27, '#4e342e', '#5d4037', '#3e2723', 0.4, 0.3);
                drawTexturedRect(-36, 24, 54, 6, '#3e2723', '#4e342e', '#2e1c15', 0.4, 0.2); // お腹の影
                
                // 尻尾
                for (let i=0; i<8; i++) {
                    let tailWig = Math.floor(Math.sin(now/150 + i)*6/3)*3;
                    drawTexturedRect(-48 - i*3, 6 + i*3 + tailWig, 6, 6, '#1a110e', '#2e1c15', '#000', 0.3, 0.2);
                }

                // 手前の足
                drawTexturedRect(-27, 15, 12, 27 - legAnim1, '#4e342e', '#5d4037', '#3e2723', 0.4, 0.2);
                drawTexturedRect(15, 15, 12, 27 - legAnim2, '#4e342e', '#5d4037', '#3e2723', 0.4, 0.2);
                ctx.fillStyle = '#222'; ctx.fillRect(-27, 42 - legAnim1, 15, 6); ctx.fillRect(15, 42 - legAnim2, 15, 6);

                // 馬鎧
                drawTexturedRect(-42, 3, 63, 6, '#b7950b', '#f1c40f', '#9a7d0a', 0.5, 0.2);
                drawTexturedRect(-30, 9, 36, 12, '#7b241c', '#922b21', '#641e16', 0.4, 0.2);

                // 上半身（オーク）
                drawTexturedRect(6, -33, 27, 36, '#a04000', '#d35400', '#873600', 0.4, 0.2);
                drawTexturedRect(12, -27, 18, 15, '#d35400', '#e67e22', '#ba4a00', 0.4, 0.2); // 胸筋
                
                // 兜と頭部
                drawTexturedRect(12, -51, 21, 18, '#1c2833', '#2c3e50', '#151e26', 0.4, 0.2);
                drawTexturedRect(15, -51, 12, 9, '#2c3e50', '#34495e', '#212f3d', 0.4, 0.2);
                
                // 兜の角
                for (let i=0; i<5; i++) {
                    ctx.fillStyle = '#d0d3d4';
                    ctx.fillRect(15 - i*6, -51 - i*3, 6, 6);
                    ctx.fillRect(27 + i*6, -51 - i*3, 6, 6);
                }
                
                // 目
                ctx.fillStyle = '#111'; ctx.fillRect(15, -42, 15, 6);
                ctx.shadowBlur = 10; ctx.shadowColor = 'red'; ctx.fillStyle = '#ff3333';
                ctx.fillRect(21, -42, 3, 3);
                ctx.shadowBlur = 0; ctx.fillStyle = '#fff'; ctx.fillRect(21, -42, 3, 3);

                // 腕と巨大斧
                ctx.save();
                ctx.translate(15, -21);
                if(b.state === 'axe_prepare') ctx.rotate(-Math.PI/2.5);
                else if (b.state === 'axe_swing') {
                    ctx.rotate(Math.PI/1.1);
                    ctx.fillStyle = 'rgba(231, 76, 60, 0.7)';
                    ctx.beginPath(); ctx.moveTo(0,0); ctx.arc(0, 0, TS * 3.0, -Math.PI/2, Math.PI/2); ctx.fill();
                } else if (b.state === 'charge_prepare' || b.state === 'charge') {
                    ctx.rotate(Math.PI/3);
                }
                
                // 斧の柄
                drawTexturedRect(-6, -45, 9, 81, '#3e2723', '#5d4037', '#261611', 0.4, 0.2);
                // 斧の刃
                drawTexturedRect(-15, -45, 30, 24, '#566573', '#7f8c8d', '#2c3e50', 0.4, 0.2);
                drawTexturedRect(-27, -42, 54, 18, '#566573', '#7f8c8d', '#2c3e50', 0.4, 0.2);
                drawTexturedRect(-39, -36, 78, 12, '#566573', '#7f8c8d', '#2c3e50', 0.4, 0.2);
                ctx.fillStyle = '#bdc3c7'; ctx.fillRect(-39, -39, 78, 3); // ハイライト
                
                ctx.fillStyle = 'rgba(139, 0, 0, 0.7)'; ctx.fillRect(21, -36, 18, 6); // 血のり
                
                // 握る手
                drawTexturedRect(-6, 6, 15, 12, '#e67e22', '#d35400', '#ba4a00', 0.4, 0.2);
                // 肩当て
                drawTexturedRect(-15, -15, 24, 12, '#34495e', '#5d6d7e', '#2c3e50', 0.4, 0.2);
                ctx.fillStyle = '#bdc3c7'; ctx.fillRect(-12, -21, 6, 6); ctx.fillRect(0, -21, 6, 6);
                
                ctx.restore();
                ctx.restore();
            }
            
            ctx.fillStyle = 'black';
            ctx.fillRect(bx - 30, by - 65, 60, 6);
            ctx.fillStyle = '#2ecc71';
            ctx.fillRect(bx - 30, by - 65, 60 * (b.hp / b.maxHp), 6);
            ctx.fillStyle = 'white';
            ctx.font = 'bold 12px Inter';
            ctx.textAlign = 'center';
            ctx.fillText(b.name, bx, by - 72);
        }
    };

    bosses.forEach(drawBoss);
    drawBoss(finalBoss);

    // カエルの描画
    frogs.forEach(f => {
        let mapFx = Math.floor(f.x);
        let mapFy = Math.floor(f.y);
        if (mapFy >= 0 && mapFy < MAP_H && mapFx >= 0 && mapFx < MAP_W) {
            if (!explored[mapFy][mapFx]) return;
        }
        let fx = f.x * TS + TS/2 - currentCamX;
        let fy = f.y * TS + TS/2 - currentCamY - (f.z || 0);

        ctx.fillStyle = 'rgba(0,0,0,0.4)';
        ctx.beginPath(); ctx.ellipse(fx, f.y * TS + TS/2 - currentCamY + 8, 12, 4, 0, 0, Math.PI*2); ctx.fill();

        ctx.save();
        ctx.translate(fx, fy);
        if (f.facing < 0) ctx.scale(-1, 1);

        if (f.petrifyTimer > 0) {
            ctx.fillStyle = '#ffffff';
            ctx.fillRect(-10, -10, 20, 18);
            ctx.strokeStyle = '#cbd5e1';
            ctx.lineWidth = 1.5;
            ctx.strokeRect(-10, -10, 20, 18);
        } else {
            ctx.fillStyle = '#27ae60';
            ctx.fillRect(-10, -6, 20, 14);
            ctx.fillStyle = '#58d68d';
            ctx.fillRect(-8, -4, 14, 6);
            ctx.fillStyle = 'white';
            ctx.fillRect(3, -10, 5, 5);
            ctx.fillStyle = f.hostile ? '#e74c3c' : '#111';
            ctx.fillRect(5, -9, 3, 3);

            if (f.state === 'spit_prepare') {
                ctx.fillStyle = '#1abc9c';
                ctx.beginPath(); ctx.arc(12, 0, 4 + Math.sin(now/50)*2, 0, Math.PI*2); ctx.fill();
            }
        }
        ctx.restore();
        
        if (f.hostile) {
            ctx.fillStyle = 'black';
            ctx.fillRect(fx - 15, fy - 20, 30, 4);
            ctx.fillStyle = '#e74c3c';
            ctx.fillRect(fx - 15, fy - 20, 30 * (f.hp / f.maxHp), 4);
        }
    });

    // 山羊の描画
    goats.forEach(g => {
        let mapGx = Math.floor(g.x);
        let mapGy = Math.floor(g.y);
        if (mapGy >= 0 && mapGy < MAP_H && mapGx >= 0 && mapGx < MAP_W) {
            if (!explored[mapGy][mapGx]) return;
        }
        let gx = g.x * TS + TS/2 - currentCamX;
        let gy = g.y * TS + TS/2 - currentCamY;

        ctx.fillStyle = 'rgba(0,0,0,0.4)';
        ctx.beginPath(); ctx.ellipse(gx, gy + 12, 14, 5, 0, 0, Math.PI*2); ctx.fill();

        ctx.save();
        ctx.translate(gx, gy);
        if (g.facing < 0) ctx.scale(-1, 1);

        if (g.petrifyTimer > 0) {
            ctx.fillStyle = '#ffffff';
            ctx.fillRect(-12, -12, 24, 26);
            ctx.strokeStyle = '#cbd5e1';
            ctx.lineWidth = 1.5;
            ctx.strokeRect(-12, -12, 24, 26);
        } else {
            ctx.fillStyle = '#ecf0f1';
            ctx.fillRect(-12, -6, 24, 14);
            ctx.fillStyle = '#bdc3c7';
            ctx.fillRect(-10, 8, 4, 6);
            ctx.fillRect(6, 8, 4, 6);
            ctx.fillRect(8, -12, 10, 10);
            ctx.fillStyle = '#7f8c8d';
            ctx.fillRect(5, -16, 6, 4);
            ctx.fillStyle = g.hostile ? '#e74c3c' : '#2c3e50';
            ctx.fillRect(14, -9, 3, 3);
        }

        ctx.restore();

        if (g.hostile) {
            ctx.fillStyle = 'black';
            ctx.fillRect(gx - 15, gy - 22, 30, 4);
            ctx.fillStyle = '#e74c3c';
            ctx.fillRect(gx - 15, gy - 22, 30 * (g.hp / g.maxHp), 4);
        }
    });

    // ブル（牛）の描画
    bulls.forEach(b => {
        let mapBx = Math.floor(b.x);
        let mapBy = Math.floor(b.y);
        if (mapBy >= 0 && mapBy < MAP_H && mapBx >= 0 && mapBx < MAP_W) {
            if (!explored[mapBy][mapBx]) return;
        }
        let bx = b.x * TS + TS / 2 - currentCamX;
        let by = b.y * TS + TS / 2 - currentCamY;

        ctx.fillStyle = 'rgba(0,0,0,0.48)';
        ctx.beginPath(); ctx.ellipse(bx, by + 13, 17, 6, 0, 0, Math.PI * 2); ctx.fill();

        ctx.save();
        ctx.translate(bx, by);
        if (b.facing < 0) ctx.scale(-1, 1);

        if (b.petrifyTimer > 0) {
            ctx.fillStyle = '#ffffff';
            ctx.fillRect(-15, -13, 30, 27);
            ctx.strokeStyle = '#cbd5e1';
            ctx.lineWidth = 1.5;
            ctx.strokeRect(-15, -13, 30, 27);
        } else {
            let walkShift = (b.state === 'chase' || b.state === 'wander' || b.state === 'charge') ? Math.sin(now / 80) * 2.5 : 0;
            // 胴体（がっしりした茶色の牛）
            ctx.fillStyle = '#5c3a21';
            ctx.fillRect(-15, -8, 28, 17);
            ctx.fillStyle = '#784b2a';
            ctx.fillRect(-13, -6, 22, 8);
            // 脚
            ctx.fillStyle = '#3e2723';
            ctx.fillRect(-13, 9 + walkShift, 5, 7);
            ctx.fillRect(-5, 9 - walkShift, 5, 7);
            ctx.fillRect(4, 9 + walkShift, 5, 7);
            ctx.fillRect(10, 9 - walkShift, 5, 7);
            // 頭部
            ctx.fillStyle = '#4a2e1b';
            ctx.fillRect(9, -13, 12, 13);
            // 鼻輪
            ctx.fillStyle = '#d97706';
            ctx.fillRect(18, -3, 4, 3);
            // 鋭い2本のツノ
            ctx.fillStyle = '#e2e8f0';
            ctx.fillRect(14, -19, 3, 6);
            ctx.fillRect(16, -21, 3, 4);
            ctx.fillRect(9, -18, 3, 5);
            ctx.fillRect(11, -20, 3, 3);
            // 赤い目
            ctx.fillStyle = '#ef4444';
            ctx.fillRect(16, -10, 3, 3);

            // ツノはらい斬撃エフェクト
            if (b.state === 'sweep') {
                ctx.strokeStyle = 'rgba(250, 204, 21, 0.85)';
                ctx.lineWidth = 4;
                ctx.beginPath();
                ctx.arc(14, -4, 22, -Math.PI * 0.45, Math.PI * 0.45);
                ctx.stroke();
            }
        }
        ctx.restore();

        if (b.hp < b.maxHp) {
            ctx.fillStyle = 'black';
            ctx.fillRect(bx - 18, by - 26, 36, 4);
            ctx.fillStyle = '#f59e0b';
            ctx.fillRect(bx - 18, by - 26, 36 * (b.hp / b.maxHp), 4);
        }
    });

    // メイズレアンの描画（人形風）
    mazerians.forEach(m => {
        let mapMx = Math.floor(m.x);
        let mapMy = Math.floor(m.y);
        if (mapMy >= 0 && mapMy < MAP_H && mapMx >= 0 && mapMx < MAP_W) {
            if (!explored[mapMy][mapMx]) return;
        }
        let mx = m.x * TS + TS/2 - currentCamX;
        let my = m.y * TS + TS/2 - currentCamY;
        let floatY = Math.sin(now / 250 + m.x) * 3;
        let sway = Math.sin(now / 300 + m.y) * 0.15;

        ctx.save();
        ctx.translate(mx, my + floatY);

        if (m.petrifyTimer > 0) {
            ctx.fillStyle = '#ffffff';
            ctx.beginPath(); ctx.arc(0, -12, 8, 0, Math.PI * 2); ctx.fill();
            ctx.beginPath(); ctx.moveTo(0, -6); ctx.lineTo(10, 12); ctx.lineTo(-10, 12); ctx.closePath(); ctx.fill();
        } else {
            ctx.strokeStyle = 'rgba(224, 64, 251, 0.45)';
            ctx.lineWidth = 1;
            ctx.beginPath();
            ctx.moveTo(-10, -28); ctx.lineTo(-10, -4);
            ctx.moveTo(10, -28); ctx.lineTo(10, -4);
            ctx.moveTo(0, -30); ctx.lineTo(0, -16);
            ctx.stroke();

            ctx.strokeStyle = '#8e44ad';
            ctx.lineWidth = 2;
            ctx.beginPath(); ctx.moveTo(-12, -28); ctx.lineTo(12, -28); ctx.stroke();

            ctx.rotate(sway);
            ctx.strokeStyle = '#d7bde2';
            ctx.lineWidth = 3;
            ctx.beginPath();
            ctx.moveTo(-4, 6); ctx.lineTo(-6 + Math.sin(now/200)*2, 16);
            ctx.moveTo(4, 6); ctx.lineTo(6 - Math.sin(now/200)*2, 16);
            ctx.stroke();

            ctx.fillStyle = '#5b2c6f';
            ctx.beginPath(); ctx.moveTo(0, -6); ctx.lineTo(9, 8); ctx.lineTo(-9, 8); ctx.closePath(); ctx.fill();
            ctx.strokeStyle = '#e040fb'; ctx.lineWidth = 1; ctx.stroke();

            let armRaise = m.state === 'shoot_prepare' ? -8 : Math.cos(now/250)*2;
            ctx.strokeStyle = '#ebdef0'; ctx.lineWidth = 2.5;
            ctx.beginPath();
            ctx.moveTo(-5, -4); ctx.lineTo(-13, armRaise);
            ctx.moveTo(5, -4); ctx.lineTo(13, armRaise);
            ctx.stroke();

            ctx.fillStyle = '#f5eef8';
            ctx.beginPath(); ctx.arc(0, -12, 7.5, 0, Math.PI*2); ctx.fill();
            ctx.fillStyle = '#2c3e50';
            ctx.beginPath(); ctx.arc(0, -13, 8, Math.PI, 0); ctx.fill();

            ctx.fillStyle = '#e040fb';
            let eyeShift = m.facing > 0 ? 1.5 : -1.5;
            ctx.beginPath();
            ctx.arc(-2.5 + eyeShift, -11, 1.8, 0, Math.PI*2);
            ctx.arc(2.5 + eyeShift, -11, 1.8, 0, Math.PI*2);
            ctx.fill();

            if (m.state === 'shoot_prepare') {
                ctx.fillStyle = '#e040fb';
                ctx.beginPath();
                ctx.arc(-15, armRaise, 3.5, 0, Math.PI*2);
                ctx.arc(15, armRaise, 3.5, 0, Math.PI*2);
                ctx.fill();
            }
        }
        ctx.restore();

        if (m.hp < m.maxHp) {
            ctx.fillStyle = 'black';
            ctx.fillRect(mx - 15, my - 34, 30, 4);
            ctx.fillStyle = '#9b59b6';
            ctx.fillRect(mx - 15, my - 34, 30 * (m.hp / m.maxHp), 4);
        }
    });

    // 忘却の木の描画
    forgottenTrees.forEach(ft => {
        let fx = ft.x * TS + TS / 2 - currentCamX;
        let fy = ft.y * TS + TS / 2 - currentCamY;
        let wobble = ft.petrifyTimer > 0 ? 0 : Math.sin(ft.walkPhase || 0) * 3;

        ctx.fillStyle = 'rgba(0, 0, 0, 0.5)';
        ctx.beginPath(); ctx.ellipse(fx, fy + 15, 15, 5, 0, 0, Math.PI * 2); ctx.fill();

        ctx.save();
        ctx.translate(fx, fy + Math.abs(wobble) * -0.5);

        if (ft.petrifyTimer > 0) {
            // 真っ白になった状態
            ctx.fillStyle = '#ffffff';
            ctx.fillRect(-7, -14, 14, 28);
            ctx.fillRect(-14, -10, 7, 5);
            ctx.fillRect(7, -12, 7, 5);
            ctx.fillRect(-10, 12, 6, 5);
            ctx.fillRect(4, 12, 6, 5);
        } else {
            // 根っこの足
            ctx.fillStyle = '#475569';
            ctx.fillRect(-10, 10 + wobble * 0.4, 6, 6);
            ctx.fillRect(4, 10 - wobble * 0.4, 6, 6);

            // 幹本体
            ctx.fillStyle = '#64748b';
            ctx.fillRect(-7, -14, 14, 25);
            ctx.fillStyle = '#cbd5e1';
            ctx.fillRect(-4, -14, 6, 25);

            // 枯れた枝の腕
            ctx.fillStyle = '#94a3b8';
            ctx.fillRect(-14, -10, 7, 4);
            ctx.fillRect(-16, -15, 4, 6);
            ctx.fillRect(7, -12, 7, 4);
            ctx.fillRect(12, -17, 4, 6);

            // 怪しく光る目
            ctx.fillStyle = '#e040fb';
            ctx.fillRect(-4, -5, 3, 3);
            ctx.fillRect(2, -5, 3, 3);
        }
        ctx.restore();

        // 名前とHPバー
        ctx.fillStyle = 'black';
        ctx.fillRect(fx - 18, fy - 26, 36, 4);
        ctx.fillStyle = '#a855f7';
        ctx.fillRect(fx - 18, fy - 26, 36 * (ft.hp / ft.maxHp), 4);
        ctx.fillStyle = '#e2e8f0';
        ctx.font = 'bold 10px Inter';
        ctx.textAlign = 'center';
        ctx.fillText('忘却の木', fx, fy - 30);
    });

    // --- プレイヤーの描画（コアキーパー風ドット絵冒険者） ---
    let pDrawX = player.px - currentCamX;
    let pDrawY = player.py - currentCamY;
    let isPetrified = player.petrifyTimer > 0;
    let isStunned = player.stunTimer > 0;
    let isMoving = !isPetrified && !isStunned && (keys['KeyW'] || keys['KeyS'] || keys['KeyA'] || keys['KeyD'] ||
                   keys['ArrowUp'] || keys['ArrowDown'] || keys['ArrowLeft'] || keys['ArrowRight'] ||
                   (controlMode === 'mobile' && (mobileJoyVec.x !== 0 || mobileJoyVec.y !== 0)));
    let walkBob = isMoving ? Math.sin(now / 70) * 2.5 : 0;

    // 1. 足元の接地影
    ctx.fillStyle = 'rgba(0, 0, 0, 0.55)';
    ctx.beginPath();
    ctx.ellipse(pDrawX, pDrawY + 14, 13, 5, 0, 0, Math.PI * 2);
    ctx.fill();

    // 2. 冒険者のドット絵ボディ
    ctx.save();
    ctx.translate(pDrawX, pDrawY - Math.abs(walkBob));

    if (isPetrified) {
        // 白化の呪い中：全身真っ白＋行動不能カウントダウン表示
        ctx.fillStyle = '#ffffff';
        ctx.shadowBlur = 12;
        ctx.shadowColor = '#ffffff';
        ctx.fillRect(-7, 8, 5, 6);
        ctx.fillRect(2, 8, 5, 6);
        ctx.fillRect(-9, -4, 18, 13);
        ctx.fillRect(-8, -16, 16, 13);
        ctx.fillRect(-9, -18, 18, 6);
        ctx.shadowBlur = 0;
        ctx.strokeStyle = '#cbd5e1';
        ctx.lineWidth = 1.5;
        ctx.strokeRect(-9, -18, 18, 32);
    } else {
        // 足（歩くと交互に動く）
        let stepL = isMoving ? Math.sin(now / 70) * 3 : 0;
        ctx.fillStyle = '#3e2723';
        ctx.fillRect(-7, 8 + stepL, 5, 6);
        ctx.fillRect(2, 8 - stepL, 5, 6);

        // 胴体（アーマー＆マント）
        ctx.fillStyle = '#1e3a8a';
        ctx.fillRect(-9, -4, 18, 13);

        // 鎧(Chestplate)を描画
        let cp = player.equipment.chestplate;
        if (cp) {
            let t = cp.replace('chestplate_', '');
            let col = t === 'iron' ? '#cbd5e1' : (t === 'rock' ? '#64748b' : (t === 'mud' ? '#5d4037' : (t === 'ash' ? '#71717a' : (t === 'withered' ? '#475569' : (t === 'maze' ? '#a855f7' : (t === 'medusa' ? '#10b981' : '#b45309'))))));
            ctx.fillStyle = col;
            ctx.fillRect(-9, -4, 18, 13);
        } else {
            ctx.fillStyle = '#38bdf8';
            ctx.fillRect(-7, -2, 14, 9); // デフォルトの胸当て
        }
        
        ctx.fillStyle = '#92400e';
        ctx.fillRect(-9, 6, 18, 3); // ベルト
        ctx.fillStyle = '#fbbf24';
        ctx.fillRect(-2, 6, 4, 3); // バックル
        
        // 靴(Boots)を描画
        let bt = player.equipment.boots;
        if (bt) {
            let t = bt.replace('boots_', '');
            let col = t === 'iron' ? '#cbd5e1' : (t === 'rock' ? '#64748b' : (t === 'mud' ? '#5d4037' : (t === 'ash' ? '#71717a' : (t === 'withered' ? '#475569' : (t === 'maze' ? '#a855f7' : (t === 'medusa' ? '#10b981' : '#b45309'))))));
            ctx.fillStyle = col;
            ctx.fillRect(-8, 9 + stepL, 7, 5);
            ctx.fillRect(1, 9 - stepL, 7, 5);
        }

        // 頭部（肌・髪・目）
        ctx.fillStyle = '#fed7aa';
        ctx.fillRect(-8, -16, 16, 13);
        // 髪の毛（フード/冒険者ヘア）
        ctx.fillStyle = '#0d9488';
        ctx.fillRect(-9, -18, 18, 6);
        
        // 兜(Helmet)を描画
        let hm = player.equipment.helmet;
        if (hm) {
            let t = hm.replace('helmet_', '');
            let col = t === 'iron' ? '#cbd5e1' : (t === 'rock' ? '#64748b' : (t === 'mud' ? '#5d4037' : (t === 'ash' ? '#71717a' : (t === 'withered' ? '#475569' : (t === 'maze' ? '#a855f7' : (t === 'medusa' ? '#10b981' : '#b45309'))))));
            ctx.fillStyle = col;
            ctx.fillRect(-9, -20, 18, 8); // ヘルメット上部
            if (player.dir === 'up') {
                ctx.fillRect(-9, -20, 18, 17);
            } else if (player.dir === 'left' || player.dir === 'right') {
                ctx.fillRect(player.dir === 'left' ? -3 : -9, -16, 12, 10);
            }
        } else {
            if (player.dir === 'up') {
                ctx.fillRect(-9, -18, 18, 13);
            } else if (player.dir === 'left') {
                ctx.fillRect(-3, -18, 12, 12);
                ctx.fillStyle = '#111827';
                ctx.fillRect(-6, -11, 2, 3);
            } else if (player.dir === 'right') {
                ctx.fillRect(-9, -18, 12, 12);
                ctx.fillStyle = '#111827';
                ctx.fillRect(4, -11, 2, 3);
            } else {
                // 下向き（正面）
                ctx.fillRect(-9, -16, 3, 8);
                ctx.fillRect(6, -16, 3, 8);
                ctx.fillStyle = '#111827';
                ctx.fillRect(-5, -11, 2, 3);
                ctx.fillRect(3, -11, 2, 3);
            }
        }

        // 速度デバフ中の緑の粘液エフェクト
        if (player.speedDebuffTimer > 0) {
            ctx.fillStyle = 'rgba(46, 204, 113, 0.55)';
            ctx.fillRect(-10, 4, 20, 10);
        }
    }
    ctx.restore();

    if (isPetrified) {
        ctx.fillStyle = '#ffffff';
        ctx.strokeStyle = '#000000';
        ctx.lineWidth = 3;
        ctx.font = 'bold 12px Inter';
        ctx.textAlign = 'center';
        let sec = (player.petrifyTimer / 1000).toFixed(1);
        ctx.strokeText(`行動不能 (${sec}s)`, pDrawX, pDrawY - 26);
        ctx.fillText(`行動不能 (${sec}s)`, pDrawX, pDrawY - 26);
    }


    // 3. 手持ち装備・ツールの描画
    ctx.save();
    ctx.translate(pDrawX, pDrawY - Math.abs(walkBob));
    if(player.dir === 'up') ctx.rotate(Math.PI);
    else if(player.dir === 'left') ctx.rotate(Math.PI/2);
    else if(player.dir === 'right') ctx.rotate(-Math.PI/2);

    let currentItemId = player.hotbar[player.selectedSlot];

    if (isPetrified) {
        ctx.fillStyle = '#ffffff';
        ctx.fillRect(11, -12, 4, 22);
    } else if(currentItemId && (currentItemId.startsWith('pickaxe_') || currentItemId === 'maze_pickaxe')) {
        let pc = ITEM_DATA[currentItemId] ? ITEM_DATA[currentItemId].tier : 0;
    } else if (currentItemId && currentItemId.startsWith('sword_') || currentItemId === 'geera_axe') {
        let sc = ITEM_DATA[currentItemId] ? ITEM_DATA[currentItemId].tier : 0;
        let bladeCol = currentItemId === 'sword_maze' ? '#c084fc' : (currentItemId === 'sword_medusa' ? '#10b981' : (currentItemId === 'geera_axe' ? '#e67e22' : (sc >= 4 ? '#ffffff' : (sc >= 3 ? '#e2e8f0' : (sc >= 2 ? '#fbbf24' : '#bcaaa4')))));
        ctx.fillStyle = bladeCol;
        if (currentItemId === 'geera_axe') {
            ctx.fillRect(10, -8, 8, 16);
            ctx.fillStyle = '#5d4037'; ctx.fillRect(13, -12, 2, 28);
        } else {
            ctx.fillRect(12, -4, 4, 24);
            ctx.fillStyle = '#f59e0b';
            ctx.fillRect(9, -4, 10, 3);
        }
    } else if (currentItemId && currentItemId.startsWith('shield_')) {
        let shc = ITEM_DATA[currentItemId] ? ITEM_DATA[currentItemId].tier : 0;
        let shieldCol = currentItemId === 'maze_shield' ? '#312e81' : (currentItemId === 'shield_medusa' ? '#10b981' : (currentItemId.includes('iron') ? '#e2e8f0' : (currentItemId.includes('rock') ? '#94a3b8' : (currentItemId.includes('mud') ? '#5d4037' : '#d97706'))));
        let borderCol = currentItemId === 'maze_shield' ? '#c084fc' : '#475569';
        ctx.fillStyle = shieldCol;
        ctx.fillRect(10, -8, 6, 16);
        ctx.strokeStyle = borderCol;
        ctx.lineWidth = 1.5;
        ctx.strokeRect(10, -8, 6, 16);
    }
    ctx.restore();

    // 空気中の光る胞子パーティクル
    for (let i = 0; i < 18; i++) {
        let sx = ((i * 197 + Math.cos(now / 1100 + i) * 35 + canvas.width) % canvas.width);
        let sy = ((i * 313 + Math.sin(now / 900 + i) * 35 + canvas.height) % canvas.height);
        let alpha = 0.35 + Math.sin(now / 350 + i * 2) * 0.25;
        ctx.fillStyle = i % 3 === 0 ? `rgba(125, 211, 252, ${alpha})` : `rgba(253, 224, 71, ${alpha})`;
        ctx.fillRect(Math.floor(sx), Math.floor(sy), 3, 3);
    }

    // プロジェクタイル（粘液・ホーミング弾）の描画
    projectiles.forEach(p => {
        let px = p.x - currentCamX;
        let py = p.y - currentCamY;
        if (p.type === 'slime') {
            ctx.fillStyle = '#2ecc71';
            ctx.beginPath(); ctx.arc(px, py, 6 + Math.sin(now/50)*2, 0, Math.PI*2); ctx.fill();
            ctx.fillStyle = 'rgba(46, 204, 113, 0.4)';
            ctx.beginPath(); ctx.arc(px, py, 12, 0, Math.PI*2); ctx.fill();
        } else if (p.type === 'mazerian_homing') {
            ctx.fillStyle = '#e040fb';
            ctx.shadowBlur = 8; ctx.shadowColor = '#e040fb';
            ctx.beginPath(); ctx.arc(px, py, 5, 0, Math.PI*2); ctx.fill();
            ctx.shadowBlur = 0;
            ctx.fillStyle = '#ffffff';
            ctx.beginPath(); ctx.arc(px, py, 2, 0, Math.PI*2); ctx.fill();
        } else if (p.type === 'medusa_orb') {
            ctx.fillStyle = '#2ecc71';
            ctx.shadowBlur = 10; ctx.shadowColor = '#a3e635';
            ctx.beginPath(); ctx.arc(px, py, 6, 0, Math.PI*2); ctx.fill();
            ctx.shadowBlur = 0;
            ctx.fillStyle = '#fef08a';
            ctx.beginPath(); ctx.arc(px, py, 2.5, 0, Math.PI*2); ctx.fill();
        }
    });

    // ダメージテキストの描画
    damageTexts.forEach(d => {
        ctx.globalAlpha = Math.max(0, d.life);
        ctx.fillStyle = 'white';
        ctx.strokeStyle = 'black';
        ctx.lineWidth = 3;
        ctx.font = 'bold 24px Inter';
        ctx.textAlign = 'center';
        let tx = d.x - currentCamX;
        let ty = d.y - currentCamY;
        ctx.strokeText(d.amount, tx, ty);
        ctx.fillText(d.amount, tx, ty);
    });
    ctx.globalAlpha = 1.0;

    // 画像のようなリアルな光源＆周囲の深い闇（松明の暖色グロー＋石像の光）
    let pScreenX = player.px - currentCamX;
    let pScreenY = player.py - currentCamY;
    let flicker = Math.sin(now / 95) * 5 + Math.cos(now / 160) * 3;
    let lightRadius = Math.max(TS * 1.5 + 1, Math.max(canvas.width, canvas.height) * 0.58 + flicker);

    // 1. 周囲を包み込む深い闇と暖色ライティング
    let lightGrad = ctx.createRadialGradient(pScreenX, pScreenY, TS * 1.5, pScreenX, pScreenY, lightRadius);
    lightGrad.addColorStop(0, 'rgba(255, 190, 105, 0.14)');
    lightGrad.addColorStop(0.32, 'rgba(220, 110, 55, 0.08)');
    lightGrad.addColorStop(0.68, 'rgba(25, 12, 10, 0.48)');
    lightGrad.addColorStop(1, 'rgba(4, 2, 3, 0.88)');
    ctx.fillStyle = lightGrad;
    ctx.fillRect(0, 0, canvas.width, canvas.height);

    // 2. プレイヤーの手元の松明オーラ（加算合成で地面を暖かく照らす）
    ctx.save();
    ctx.globalCompositeOperation = 'lighter';
    let torchRadius = Math.max(4.1, TS * 3.8 + flicker);
    let torchGrad = ctx.createRadialGradient(pScreenX, pScreenY - 4, 4, pScreenX, pScreenY - 4, torchRadius);
    torchGrad.addColorStop(0, 'rgba(255, 175, 75, 0.22)');
    torchGrad.addColorStop(0.5, 'rgba(255, 120, 40, 0.08)');
    torchGrad.addColorStop(1, 'rgba(255, 100, 30, 0)');
    ctx.fillStyle = torchGrad;
    ctx.beginPath();
    ctx.arc(pScreenX, pScreenY - 4, torchRadius, 0, Math.PI * 2);
    ctx.fill();

    // 3. 石像の周囲にも発光オーラを追加
    let sGradRadius = Math.max(10.1, TS * 4.5);
    statues.forEach(s => {
        if (map[s.y][s.x] !== TILE.FLOOR && map[s.y][s.x] !== TILE.HAY && map[s.y][s.x] !== TILE.ASH_FLOOR) return;
        if (!explored[s.y] || !explored[s.y][s.x]) return;
        let sx = s.x * TS + TS / 2 - currentCamX;
        let sy = s.y * TS + TS / 2 - currentCamY;
        if (sx < -200 || sx > canvas.width + 200 || sy < -200 || sy > canvas.height + 200) return;
        let sGrad = ctx.createRadialGradient(sx, sy, 10, sx, sy, sGradRadius);
        sGrad.addColorStop(0, 'rgba(255, 185, 80, 0.24)');
        sGrad.addColorStop(0.5, 'rgba(255, 130, 45, 0.08)');
        sGrad.addColorStop(1, 'rgba(0, 0, 0, 0)');
        ctx.fillStyle = sGrad;
        ctx.beginPath();
        ctx.arc(sx, sy, sGradRadius, 0, Math.PI * 2);
        ctx.fill();
    });
    ctx.restore();

    // 白化の呪い発動時の真っ白フラッシュ演出
    if (whiteFlashAlpha > 0) {
        ctx.fillStyle = `rgba(255, 255, 255, ${whiteFlashAlpha})`;
        ctx.fillRect(0, 0, canvas.width, canvas.height);
    }
}

function drawMinimap() {
    if (minimapZoomLevel === 3) {
        let container = document.getElementById('minimapContainer');
        if (container && container.clientWidth > 0 && container.clientHeight > 0) {
            if (mCanvas.width !== container.clientWidth || mCanvas.height !== container.clientHeight) {
                mCanvas.width = container.clientWidth;
                mCanvas.height = container.clientHeight;
            }
        }
    }

    let w = mCanvas.width;
    let h = mCanvas.height;
    mCtx.fillStyle = '#050508';
    mCtx.fillRect(0, 0, w, h);

    let pCol = Math.floor(player.px / TS);
    let pRow = Math.floor(player.py / TS);

    let scale, originX, originY, startX, endX, startY, endY;

    if (minimapZoomLevel === 3) {
        let baseScale = Math.min(w, h) / MAP_W;
        scale = baseScale * fullMapZoom;
        originX = w / 2 - fullMapCenterX * scale;
        originY = h / 2 - fullMapCenterY * scale;

        startX = Math.max(0, Math.floor(-originX / scale));
        endX = Math.min(MAP_W, Math.ceil((w - originX) / scale));
        startY = Math.max(0, Math.floor(-originY / scale));
        endY = Math.min(MAP_H, Math.ceil((h - originY) / scale));

        // 最初の石像を中心とした円形ワールドの背景
        let worldCenterX = originX + statues[0].x * scale;
        let worldCenterY = originY + statues[0].y * scale;
        let worldR = WORLD_RADIUS * scale;
        mCtx.fillStyle = '#0b0c12';
        mCtx.beginPath();
        mCtx.arc(worldCenterX, worldCenterY, worldR, 0, Math.PI * 2);
        mCtx.fill();
    } else {
        let viewTiles = minimapZoomLevel === 1 ? 80 : 160;
        startX = Math.max(0, Math.min(MAP_W - viewTiles, pCol - Math.floor(viewTiles / 2)));
        startY = Math.max(0, Math.min(MAP_H - viewTiles, pRow - Math.floor(viewTiles / 2)));
        endX = Math.min(MAP_W, startX + viewTiles);
        endY = Math.min(MAP_H, startY + viewTiles);
        scale = w / viewTiles;
        originX = -startX * scale;
        originY = -startY * scale;
    }

    for (let y = startY; y < endY; y++) {
        for (let x = startX; x < endX; x++) {
            if (!explored[y] || !explored[y][x]) continue;
            let t = map[y][x];
            if (t === TILE.VOID) continue;
            if (t === TILE.FLOOR) mCtx.fillStyle = '#5c3c2e';
            else if (t === TILE.HAY) mCtx.fillStyle = '#d4ac0d'; // 干し草を黄色で表示
            else if (t === TILE.ASH_FLOOR) mCtx.fillStyle = '#52565e';
            else if (t === TILE.WITHERED_TREE) mCtx.fillStyle = '#cbd5e1';
            else if (t === TILE.ASH) mCtx.fillStyle = '#94a3b8';
            else if (t === TILE.DIRT || t === TILE.BOMB || t === TILE.BOMB_ACTIVE) mCtx.fillStyle = '#8b4513';
            else if (t === TILE.DEEP_DIRT) mCtx.fillStyle = '#3e2723';
            else if (t === TILE.ROCK) mCtx.fillStyle = '#7f8c8d';
            else if (t === TILE.IRON) mCtx.fillStyle = '#5dade2';
            else if (t === TILE.MAZE_BRICK || t === TILE.MAZE_BLOCK || t === TILE.MAZE_UNREAL) mCtx.fillStyle = '#8e44ad';
            else if (t === TILE.WATER || t === TILE.SWAMP_WATER) mCtx.fillStyle = '#2980b9';
            else if (t === TILE.MUD) mCtx.fillStyle = '#4e342e';
            else mCtx.fillStyle = '#4a3b32';

            mCtx.fillRect(originX + x * scale, originY + y * scale, Math.ceil(scale), Math.ceil(scale));
        }
    }

    // 最初の石像を中心とした円形ワールドの外周リングを描画
    let worldCenterX = originX + statues[0].x * scale;
    let worldCenterY = originY + statues[0].y * scale;
    let worldR = WORLD_RADIUS * scale;
    mCtx.strokeStyle = minimapZoomLevel === 3 ? 'rgba(56, 189, 248, 0.8)' : 'rgba(56, 189, 248, 0.45)';
    mCtx.lineWidth = minimapZoomLevel === 3 ? 2 : 1.5;
    mCtx.beginPath();
    mCtx.arc(worldCenterX, worldCenterY, worldR, 0, Math.PI * 2);
    mCtx.stroke();

    // 石像マーカー
    statues.forEach(s => {
        if (!explored[s.y] || !explored[s.y][s.x]) return;
        mCtx.fillStyle = s.type === 'center' ? '#facc15' : '#22d3ee';
        mCtx.beginPath();
        mCtx.arc(originX + s.x * scale, originY + s.y * scale, Math.max(3, scale * 1.2), 0, Math.PI * 2);
        mCtx.fill();
    });

    // メデューサなどのボスマーカー（探索済みなら表示）
    bosses.forEach(b => {
        let showBoss = b.active && !b.dead && explored[Math.floor(b.y)] && explored[Math.floor(b.y)][Math.floor(b.x)];
        if (b.id === 'medusa' && player.hasWitheredCompass && b.active && !b.dead) {
            showBoss = true;
        }
        if (showBoss) {
            mCtx.fillStyle = b.id === 'medusa' ? '#2ecc71' : '#f97316';
            mCtx.beginPath();
            mCtx.arc(originX + b.x * scale, originY + b.y * scale, Math.max(3.5, scale * 1.4), 0, Math.PI * 2);
            mCtx.fill();
        }
    });

    // 迷宮のピン
    if (player.hasMazePin) {
        mCtx.fillStyle = '#e040fb';
        mCtx.beginPath();
        mCtx.arc(originX + bosses[0].x * scale, originY + bosses[0].y * scale, Math.max(4, scale * 1.3), 0, Math.PI * 2);
        mCtx.fill();
    }

    // ヘイザのコンパス
    if (player.hasHeizaCompass) {
        let heiza = npcs.find(n => n.type === 'heiza');
        if (heiza) {
            mCtx.fillStyle = '#facc15';
            mCtx.beginPath();
            mCtx.arc(originX + Math.floor(heiza.x / TS) * scale, originY + Math.floor(heiza.y / TS) * scale, Math.max(4, scale * 1.3), 0, Math.PI * 2);
            mCtx.fill();
        }
    }

    // ラスボスコンパス
    if (player.hasCompass && finalBoss.active && !finalBoss.dead) {
        mCtx.fillStyle = '#a855f7';
        mCtx.beginPath();
        mCtx.arc(originX + finalBoss.x * scale, originY + finalBoss.y * scale, Math.max(4.5, scale * 1.5), 0, Math.PI * 2);
        mCtx.fill();
    }

    // プレイヤー自身
    mCtx.fillStyle = '#ffffff';
    mCtx.beginPath();
    mCtx.arc(originX + pCol * scale, originY + pRow * scale, Math.max(3.5, scale * 1.3), 0, Math.PI * 2);
    mCtx.fill();
}

let lastTime = performance.now();
function gameLoop(time) {
    requestAnimationFrame(gameLoop);
    
    let dt = time - lastTime;
    lastTime = time;
    if(dt > 100) dt = 100; // タブ落ち時の巨大dtを防ぐ

    if(!isPaused) {
        updatePlayer(dt);
        if (controlMode === 'mobile' && mobileActionHeld) {
            let now = Date.now();
            let currentItem = player.hotbar[player.selectedSlot];
            let cooldown = (currentItem && currentItem.startsWith('pickaxe_')) || currentItem === 'maze_pickaxe' ? 200 : 400;
            if (now - lastActionTime > cooldown) {
                lastActionTime = now;
                performAction();
            }
        }
        updateBosses(dt);
        updateGoats(dt); // 山羊の更新処理を追加
        updateBulls(dt); // ブルの更新処理を追加
        updateFrogs(dt); // カエルの更新処理を追加
        updateMazerians(dt); // メイズレアンの更新処理を追加
        updateForgottenTrees(dt); // 忘却の木の更新処理を追加
        updateProjectiles(dt); // 飛び道具の更新処理を追加
        updateBombs(dt);
        updateMazeUnreals(dt); // 装置の更新を追加
        updateChests(dt); // チェストの更新を追加
        updateDroppedItems(dt); // ドロップアイテムの更新を追加
        updateExplored();
        checkInteractTarget(); // 毎フレームインタラクト対象をチェック
        
        // 座標表示の更新
        let pCol = Math.floor(player.px / TS);
        let pRow = Math.floor(player.py / TS);
        document.getElementById('coordDisplay').innerText = `${pCol}, ${pRow}`;
    }
    
    // ポーズ中（死亡時など）もビジュアルエフェクトは更新し、画面が完全に固まらないようにする
    updateVisuals(dt);
    
    drawGame();
    drawDroppedItems(); // ドロップアイテムの描画を追加
    drawMinimap();
}

// --- 初期化 ---
initMap();
initMobileControls();
initFullMapInteractions();
updateSettingsUI();
applyControlModeUI();
updateHUD(); // 初期HUD更新
requestAnimationFrame(gameLoop);
